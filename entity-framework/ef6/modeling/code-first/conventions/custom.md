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
# <a name="custom-code-first-conventions"></a><span data-ttu-id="bd1af-102">自訂程式碼的第一個慣例</span><span class="sxs-lookup"><span data-stu-id="bd1af-102">Custom Code First Conventions</span></span>
> [!NOTE]
> <span data-ttu-id="bd1af-103">**僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。</span><span class="sxs-lookup"><span data-stu-id="bd1af-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="bd1af-104">如果您使用的是較早版本，則不適用部分或全部的資訊。</span><span class="sxs-lookup"><span data-stu-id="bd1af-104">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="bd1af-105">當使用 Code First 模型會計算從您使用一組慣例的類別。</span><span class="sxs-lookup"><span data-stu-id="bd1af-105">When using Code First your model is calculated from your classes using a set of conventions.</span></span> <span data-ttu-id="bd1af-106">預設值[程式碼的第一個慣例](~/ef6/modeling/code-first/conventions/built-in.md)判斷項目，例如其屬性變成實體的主索引鍵]、 [實體對應至，資料表和哪些有效位數和小數位數的十進位資料行有預設的名稱。</span><span class="sxs-lookup"><span data-stu-id="bd1af-106">The default [Code First Conventions](~/ef6/modeling/code-first/conventions/built-in.md) determine things like which property becomes the primary key of an entity, the name of the table an entity maps to, and what precision and scale a decimal column has by default.</span></span>

<span data-ttu-id="bd1af-107">有時候這些預設慣例不適合您的模型，而且您必須藉由設定許多使用資料註解或 Fluent API 的個別實體可以解決它們。</span><span class="sxs-lookup"><span data-stu-id="bd1af-107">Sometimes these default conventions are not ideal for your model, and you have to work around them by configuring many individual entities using Data Annotations or the Fluent API.</span></span> <span data-ttu-id="bd1af-108">自訂程式碼的第一個慣例可讓您定義自己提供組態的預設值適用於您機型的慣例。</span><span class="sxs-lookup"><span data-stu-id="bd1af-108">Custom Code First Conventions let you define your own conventions that provide configuration defaults for your model.</span></span> <span data-ttu-id="bd1af-109">在本逐步解說中，我們將探討不同類型的自訂慣例，以及如何建立每個。</span><span class="sxs-lookup"><span data-stu-id="bd1af-109">In this walkthrough, we will explore the different types of custom conventions and how to create each of them.</span></span>


## <a name="model-based-conventions"></a><span data-ttu-id="bd1af-110">以模型為基礎的慣例</span><span class="sxs-lookup"><span data-stu-id="bd1af-110">Model-Based Conventions</span></span>

<span data-ttu-id="bd1af-111">本頁涵蓋自訂慣例 DbModelBuilder API。</span><span class="sxs-lookup"><span data-stu-id="bd1af-111">This page covers the DbModelBuilder API for custom conventions.</span></span> <span data-ttu-id="bd1af-112">此 API 應該就足以撰寫大部分的自訂慣例。</span><span class="sxs-lookup"><span data-stu-id="bd1af-112">This API should be sufficient for authoring most custom conventions.</span></span> <span data-ttu-id="bd1af-113">不過，還有能夠撰寫模型為基礎的慣例-操作最終模型，建立之後的慣例來處理進階的案例。</span><span class="sxs-lookup"><span data-stu-id="bd1af-113">However, there is also the ability to author model-based conventions - conventions that manipulate the final model once it is created - to handle advanced scenarios.</span></span> <span data-ttu-id="bd1af-114">如需詳細資訊，請參閱 <<c0> [ 模型為基礎的慣例](~/ef6/modeling/code-first/conventions/model.md)。</span><span class="sxs-lookup"><span data-stu-id="bd1af-114">For more information, see [Model-Based Conventions](~/ef6/modeling/code-first/conventions/model.md).</span></span>

 

## <a name="our-model"></a><span data-ttu-id="bd1af-115">我們的模型</span><span class="sxs-lookup"><span data-stu-id="bd1af-115">Our Model</span></span>

<span data-ttu-id="bd1af-116">一開始先定義一個簡單的模型，我們可以使用我們的慣例。</span><span class="sxs-lookup"><span data-stu-id="bd1af-116">Let's start by defining a simple model that we can use with our conventions.</span></span> <span data-ttu-id="bd1af-117">將下列類別新增至您的專案。</span><span class="sxs-lookup"><span data-stu-id="bd1af-117">Add the following classes to your project.</span></span>

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

 

## <a name="introducing-custom-conventions"></a><span data-ttu-id="bd1af-118">介紹自訂慣例</span><span class="sxs-lookup"><span data-stu-id="bd1af-118">Introducing Custom Conventions</span></span>

<span data-ttu-id="bd1af-119">讓我們撰寫會設定名為實體型別的主索引鍵的索引鍵的任何屬性的慣例。</span><span class="sxs-lookup"><span data-stu-id="bd1af-119">Let’s write a convention that configures any property named Key to be the primary key for its entity type.</span></span>

<span data-ttu-id="bd1af-120">模型產生器，可以藉由覆寫內容中的 OnModelCreating 存取已啟用慣例。</span><span class="sxs-lookup"><span data-stu-id="bd1af-120">Conventions are enabled on the model builder, which can be accessed by overriding OnModelCreating in the context.</span></span> <span data-ttu-id="bd1af-121">更新 ProductContext 類別如下所示：</span><span class="sxs-lookup"><span data-stu-id="bd1af-121">Update the ProductContext class as follows:</span></span>

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

<span data-ttu-id="bd1af-122">現在，我們名為索引鍵的模型中的任何屬性會設定為任何實體的主索引鍵的一部分。</span><span class="sxs-lookup"><span data-stu-id="bd1af-122">Now, any property in our model named Key will be configured as the primary key of whatever entity its part of.</span></span>

<span data-ttu-id="bd1af-123">我們也可以讓我們慣例具體方法是透過篩選，我們要設定屬性的型別：</span><span class="sxs-lookup"><span data-stu-id="bd1af-123">We could also make our conventions more specific by filtering on the type of property that we are going to configure:</span></span>

``` csharp
    modelBuilder.Properties<int>()
                .Where(p => p.Name == "Key")
                .Configure(p => p.IsKey());
```

<span data-ttu-id="bd1af-124">這會設定所有的屬性稱為索引鍵值必須是主要索引鍵的實體，但前提是它們是整數。</span><span class="sxs-lookup"><span data-stu-id="bd1af-124">This will configure all properties called Key to be the primary key of their entity, but only if they are an integer.</span></span>

<span data-ttu-id="bd1af-125">IsKey 方法的一個有趣的功能是它是加總。</span><span class="sxs-lookup"><span data-stu-id="bd1af-125">An interesting feature of the IsKey method is that it is additive.</span></span> <span data-ttu-id="bd1af-126">這表示如果您在多個屬性上呼叫 IsKey，它們將所有複合的索引鍵的一部分。</span><span class="sxs-lookup"><span data-stu-id="bd1af-126">Which means that if you call IsKey on multiple properties and they will all become part of a composite key.</span></span> <span data-ttu-id="bd1af-127">有一點要注意的是，當您指定多個屬性的索引鍵時您也必須指定這些屬性的順序。</span><span class="sxs-lookup"><span data-stu-id="bd1af-127">The one caveat for this is that when you specify multiple properties for a key you must also specify an order for those properties.</span></span> <span data-ttu-id="bd1af-128">您可以藉由呼叫方法，如下所示 HasColumnOrder 來這樣做：</span><span class="sxs-lookup"><span data-stu-id="bd1af-128">You can do this by calling the HasColumnOrder method like below:</span></span>

``` csharp
    modelBuilder.Properties<int>()
                .Where(x => x.Name == "Key")
                .Configure(x => x.IsKey().HasColumnOrder(1));

    modelBuilder.Properties()
                .Where(x => x.Name == "Name")
                .Configure(x => x.IsKey().HasColumnOrder(2));
```

<span data-ttu-id="bd1af-129">此程式碼會設定在我們的模型有 int 索引鍵資料行和字串名稱資料行所組成的複合索引鍵的類型。</span><span class="sxs-lookup"><span data-stu-id="bd1af-129">This code will configure the types in our model to have a composite key consisting of the int Key column and the string Name column.</span></span> <span data-ttu-id="bd1af-130">如果我們在設計工具中檢視模型看起來應該像這樣：</span><span class="sxs-lookup"><span data-stu-id="bd1af-130">If we view the model in the designer it would look like this:</span></span>

![複合索引鍵](~/ef6/media/compositekey.png)

<span data-ttu-id="bd1af-132">屬性的慣例的另一個範例是設定在我的模型對應至 SQL Server 中的 datetime2 類型，而不是日期時間中的所有日期時間屬性。</span><span class="sxs-lookup"><span data-stu-id="bd1af-132">Another example of property conventions is to configure all DateTime properties in my model to map to the datetime2 type in SQL Server instead of datetime.</span></span> <span data-ttu-id="bd1af-133">您可以使用下列來達到此目的：</span><span class="sxs-lookup"><span data-stu-id="bd1af-133">You can achieve this with the following:</span></span>

``` csharp
    modelBuilder.Properties<DateTime>()
                .Configure(c => c.HasColumnType("datetime2"));
```

 

## <a name="convention-classes"></a><span data-ttu-id="bd1af-134">慣例類別</span><span class="sxs-lookup"><span data-stu-id="bd1af-134">Convention Classes</span></span>

<span data-ttu-id="bd1af-135">另一種定義慣例是使用慣例的類別，來封裝您的慣例。</span><span class="sxs-lookup"><span data-stu-id="bd1af-135">Another way of defining conventions is to use a Convention Class to encapsulate your convention.</span></span> <span data-ttu-id="bd1af-136">使用慣例的類別時您會建立繼承自慣例 System.Data.Entity.ModelConfiguration.Conventions 命名空間中類別的型別。</span><span class="sxs-lookup"><span data-stu-id="bd1af-136">When using a Convention Class then you create a type that inherits from the Convention class in the System.Data.Entity.ModelConfiguration.Conventions namespace.</span></span>

<span data-ttu-id="bd1af-137">我們可以使用我們先前透過下列方式顯示的 datetime2 慣例來建立慣例類別：</span><span class="sxs-lookup"><span data-stu-id="bd1af-137">We can create a Convention Class with the datetime2 convention that we showed earlier by doing the following:</span></span>

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

<span data-ttu-id="bd1af-138">告知 EF 來使用此慣例，將它新增至慣例集合在 OnModelCreating，如果您已遵循本逐步解說會看起來像這樣：</span><span class="sxs-lookup"><span data-stu-id="bd1af-138">To tell EF to use this convention you add it to the Conventions collection in OnModelCreating, which if you’ve been following along with the walkthrough will look like this:</span></span>

``` csharp
    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Properties<int>()
                    .Where(p => p.Name.EndsWith("Key"))
                    .Configure(p => p.IsKey());

        modelBuilder.Conventions.Add(new DateTime2Convention());
    }
```

<span data-ttu-id="bd1af-139">您可以看到我們加入我們的執行個體至慣例集合。</span><span class="sxs-lookup"><span data-stu-id="bd1af-139">As you can see we add an instance of our convention to the conventions collection.</span></span> <span data-ttu-id="bd1af-140">繼承自慣例提供方便的分組和共用跨小組或專案的慣例。</span><span class="sxs-lookup"><span data-stu-id="bd1af-140">Inheriting from Convention provides a convenient way of grouping and sharing conventions across teams or projects.</span></span> <span data-ttu-id="bd1af-141">例如，您就可以讓一組常用的組織的所有專案使用的慣例的類別程式庫。</span><span class="sxs-lookup"><span data-stu-id="bd1af-141">You could, for example, have a class library with a common set of conventions that all of your organizations projects use.</span></span>

 

## <a name="custom-attributes"></a><span data-ttu-id="bd1af-142">自訂屬性</span><span class="sxs-lookup"><span data-stu-id="bd1af-142">Custom Attributes</span></span>

<span data-ttu-id="bd1af-143">另一個好的使用的慣例是讓新的屬性，以設定模型時使用。</span><span class="sxs-lookup"><span data-stu-id="bd1af-143">Another great use of conventions is to enable new attributes to be used when configuring a model.</span></span> <span data-ttu-id="bd1af-144">為了說明這點，讓我們建立的屬性，可用來將字串屬性標示為非 Unicode。</span><span class="sxs-lookup"><span data-stu-id="bd1af-144">To illustrate this, let’s create an attribute that we can use to mark String properties as non-Unicode.</span></span>

``` csharp
    [AttributeUsage(AttributeTargets.Property, AllowMultiple = false)]
    public class NonUnicode : Attribute
    {
    }
```

<span data-ttu-id="bd1af-145">現在，讓我們建立的慣例，以將此屬性套用至我們的模型：</span><span class="sxs-lookup"><span data-stu-id="bd1af-145">Now, let’s create a convention to apply this attribute to our model:</span></span>

``` csharp
    modelBuilder.Properties()
                .Where(x => x.GetCustomAttributes(false).OfType<NonUnicode>().Any())
                .Configure(c => c.IsUnicode(false));
```

<span data-ttu-id="bd1af-146">使用此慣例中，我們可以新增任何的字串屬性，這表示在資料庫中的資料行的 NonUnicode 屬性將會儲存為 varchar，而不是 nvarchar。</span><span class="sxs-lookup"><span data-stu-id="bd1af-146">With this convention we can add the NonUnicode attribute to any of our string properties, which means the column in the database will be stored as varchar instead of nvarchar.</span></span>

<span data-ttu-id="bd1af-147">關於此慣例，請注意一件事是，如果將 NonUnicode 屬性放在字串屬性以外的任何項目則會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="bd1af-147">One thing to note about this convention is that if you put the NonUnicode attribute on anything other than a string property then it will throw an exception.</span></span> <span data-ttu-id="bd1af-148">它會因為您無法設定 IsUnicode 字串以外的任何型別上。</span><span class="sxs-lookup"><span data-stu-id="bd1af-148">It does this because you cannot configure IsUnicode on any type other than a string.</span></span> <span data-ttu-id="bd1af-149">如果發生這種情況，則您可以讓您的慣例更具體來說，使它會篩選掉任何不是字串的項目。</span><span class="sxs-lookup"><span data-stu-id="bd1af-149">If this happens, then you can make your convention more specific, so that it filters out anything that isn’t a string.</span></span>

<span data-ttu-id="bd1af-150">雖然上述慣例適用於定義自訂屬性沒有另一個 API，可以更容易使用，特別是當您想要從屬性類別的屬性。</span><span class="sxs-lookup"><span data-stu-id="bd1af-150">While the above convention works for defining custom attributes there is another API that can be much easier to use, especially when you want to use properties from the attribute class.</span></span>

<span data-ttu-id="bd1af-151">此範例中，我們即將更新我們的屬性，並將它變更為 IsUnicode 屬性 (attribute)，讓它看起來像這樣：</span><span class="sxs-lookup"><span data-stu-id="bd1af-151">For this example we are going to update our attribute and change it to an IsUnicode attribute, so it looks like this:</span></span>

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

<span data-ttu-id="bd1af-152">一旦我們擁有此資料庫，我們可以設定 bool 我們的屬性，以判斷屬性應該 Unicode 的慣例。</span><span class="sxs-lookup"><span data-stu-id="bd1af-152">Once we have this, we can set a bool on our attribute to tell the convention whether or not a property should be Unicode.</span></span> <span data-ttu-id="bd1af-153">我們可以在我們已藉由存取 設定類別，像這樣的 ClrProperty 的慣例來這麼做：</span><span class="sxs-lookup"><span data-stu-id="bd1af-153">We could do this in the convention we have already by accessing the ClrProperty of the configuration class like this:</span></span>

``` csharp
    modelBuilder.Properties()
                .Where(x => x.GetCustomAttributes(false).OfType<IsUnicode>().Any())
                .Configure(c => c.IsUnicode(c.ClrPropertyInfo.GetCustomAttribute<IsUnicode>().Unicode));
```

<span data-ttu-id="bd1af-154">這很簡單，但還有更簡潔的方法，使用 Having 達到這個目的的慣例 API 的方法。</span><span class="sxs-lookup"><span data-stu-id="bd1af-154">This is easy enough, but there is a more succinct way of achieving this by using the Having method of the conventions API.</span></span> <span data-ttu-id="bd1af-155">Having 方法具有參數輸入 Func&lt;PropertyInfo、 T&gt;可接受的 PropertyInfo Where 相同方法，但應該會傳回物件。</span><span class="sxs-lookup"><span data-stu-id="bd1af-155">The Having method has a parameter of type Func&lt;PropertyInfo, T&gt; which accepts the PropertyInfo the same as the Where method, but is expected to return an object.</span></span> <span data-ttu-id="bd1af-156">如果傳回的物件為 null，就不能設定屬性，這表示您可以篩選出與它的屬性一樣的位置，但它不同，因為它也會擷取傳回的物件，並將它傳遞到設定方法。</span><span class="sxs-lookup"><span data-stu-id="bd1af-156">If the returned object is null then the property will not be configured, which means you can filter out properties with it just like Where, but it is different in that it will also capture the returned object and pass it to the Configure method.</span></span> <span data-ttu-id="bd1af-157">其運作如下所示：</span><span class="sxs-lookup"><span data-stu-id="bd1af-157">This works like the following:</span></span>

``` csharp
    modelBuilder.Properties()
                .Having(x =>x.GetCustomAttributes(false).OfType<IsUnicode>().FirstOrDefault())
                .Configure((config, att) => config.IsUnicode(att.Unicode));
```

<span data-ttu-id="bd1af-158">自訂屬性不是唯一的理由来使用 Having 方法，您就很有用，您需要設定您的型別或屬性時，您要篩選上的項目原因的任何地方。</span><span class="sxs-lookup"><span data-stu-id="bd1af-158">Custom attributes are not the only reason to use the Having method, it is useful anywhere that you need to reason about something that you are filtering on when configuring your types or properties.</span></span>

 

## <a name="configuring-types"></a><span data-ttu-id="bd1af-159">設定類型</span><span class="sxs-lookup"><span data-stu-id="bd1af-159">Configuring Types</span></span>

<span data-ttu-id="bd1af-160">到目前為止所有我們慣例已針對屬性，但您的模型中設定類型的慣例 API 的另一個區域。</span><span class="sxs-lookup"><span data-stu-id="bd1af-160">So far all of our conventions have been for properties, but there is another area of the conventions API for configuring the types in your model.</span></span> <span data-ttu-id="bd1af-161">體驗類似於我們到目前為止，所看到的慣例，但內部設定的選項會在該實體，而不是屬性層級。</span><span class="sxs-lookup"><span data-stu-id="bd1af-161">The experience is similar to the conventions we have seen so far, but the options inside configure will be at the entity instead of property level.</span></span>

<span data-ttu-id="bd1af-162">型別層級的慣例可以是很有用的事情之一就變更的資料表命名慣例，將對應至現有的結構描述不同於 EF 的預設值，或建立新的資料庫使用不同的命名慣例。</span><span class="sxs-lookup"><span data-stu-id="bd1af-162">One of the things that Type level conventions can be really useful for is changing the table naming convention, either to map to an existing schema that differs from the EF default or to create a new database with a different naming convention.</span></span> <span data-ttu-id="bd1af-163">若要這樣做我們首先需要的方法，可以接受在我們的模型型別的 TypeInfo 並傳回該類型的資料表名稱應該為：</span><span class="sxs-lookup"><span data-stu-id="bd1af-163">To do this we first need a method that can accept the TypeInfo for a type in our model and return what the table name for that type should be:</span></span>

``` csharp
    private string GetTableName(Type type)
    {
        var result = Regex.Replace(type.Name, ".[A-Z]", m => m.Value[0] + "_" + m.Value[1]);

        return result.ToLower();
    }
```

<span data-ttu-id="bd1af-164">這個方法會採用型別，並傳回有底線，而不是 CamelCase 使用小寫的字串。</span><span class="sxs-lookup"><span data-stu-id="bd1af-164">This method takes a type and returns a string that uses lower case with underscores instead of CamelCase.</span></span> <span data-ttu-id="bd1af-165">在我們的模型，這表示，ProductCategory 類別會對應至資料表 product\_而不是 [ProductCategories] 類別。</span><span class="sxs-lookup"><span data-stu-id="bd1af-165">In our model this means that the ProductCategory class will be mapped to a table called product\_category instead of ProductCategories.</span></span>

<span data-ttu-id="bd1af-166">一旦我們擁有該方法我們可以像這樣的慣例中呼叫它：</span><span class="sxs-lookup"><span data-stu-id="bd1af-166">Once we have that method we can call it in a convention like this:</span></span>

``` csharp
    modelBuilder.Types()
                .Configure(c => c.ToTable(GetTableName(c.ClrType)));
```

<span data-ttu-id="bd1af-167">這個慣例會設定每個型別，在我們的模型對應至我們 GetTableName 方法所傳回的資料表名稱。</span><span class="sxs-lookup"><span data-stu-id="bd1af-167">This convention configures every type in our model to map to the table name that is returned from our GetTableName method.</span></span> <span data-ttu-id="bd1af-168">此規範相當於使用 Fluent API 模型中的每個實體呼叫 ToTable 方法。</span><span class="sxs-lookup"><span data-stu-id="bd1af-168">This convention is the equivalent to calling the ToTable method for each entity in the model using the Fluent API.</span></span>

<span data-ttu-id="bd1af-169">要注意一點是，當您呼叫 ToTable EF 會需要您提供完整的資料表名稱，不含任何它通常會執行時判斷資料表名稱的複數表示為字串。</span><span class="sxs-lookup"><span data-stu-id="bd1af-169">One thing to note about this is that when you call ToTable EF will take the string that you provide as the exact table name, without any of the pluralization that it would normally do when determining table names.</span></span> <span data-ttu-id="bd1af-170">這就是為什麼我們慣例的資料表名稱是產品\_類別，而不是產品\_類別。</span><span class="sxs-lookup"><span data-stu-id="bd1af-170">This is why the table name from our convention is product\_category instead of product\_categories.</span></span> <span data-ttu-id="bd1af-171">我們可以解決問題，在我們自己進行複數表示服務的呼叫。</span><span class="sxs-lookup"><span data-stu-id="bd1af-171">We can resolve that in our convention by making a call to the pluralization service ourselves.</span></span>

<span data-ttu-id="bd1af-172">我們將使用下列程式碼[相依性解析](~/ef6/fundamentals/configuring/dependency-resolution.md)擷取複數表示服務會使用 EF 的 EF6 中新增的功能和複數化我們資料表的名稱。</span><span class="sxs-lookup"><span data-stu-id="bd1af-172">In the following code we will use the [Dependency Resolution](~/ef6/fundamentals/configuring/dependency-resolution.md) feature added in EF6 to retrieve the pluralization service that EF would have used and pluralize our table name.</span></span>

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
> <span data-ttu-id="bd1af-173">GetService 的泛型版本是擴充方法 System.Data.Entity.Infrastructure.DependencyResolution 命名空間中的，您必須新增 using 陳述式，您才能使用它的內容。</span><span class="sxs-lookup"><span data-stu-id="bd1af-173">The generic version of GetService is an extension method in the System.Data.Entity.Infrastructure.DependencyResolution namespace, you will need to add a using statement to your context in order to use it.</span></span>

### <a name="totable-and-inheritance"></a><span data-ttu-id="bd1af-174">ToTable 和繼承</span><span class="sxs-lookup"><span data-stu-id="bd1af-174">ToTable and Inheritance</span></span>

<span data-ttu-id="bd1af-175">ToTable 的另一個重要層面是，如果您必須明確地將型別對應至指定的資料表，則您可以變更將會使用 EF 的對應策略。</span><span class="sxs-lookup"><span data-stu-id="bd1af-175">Another important aspect of ToTable is that if you explicitly map a type to a given table, then you can alter the mapping strategy that EF will use.</span></span> <span data-ttu-id="bd1af-176">如果您呼叫 ToTable 繼承階層架構中的每一個型別時，傳遞的型別名稱做為資料表的名稱，如同上述，則您將會變更預設值表-table-per Hierarchy (TPH) 對應策略以每一類一表 (TPT)。</span><span class="sxs-lookup"><span data-stu-id="bd1af-176">If you call ToTable for every type in an inheritance hierarchy, passing the type name as the name of the table like we did above, then you will change the default Table-Per-Hierarchy (TPH) mapping strategy to Table-Per-Type (TPT).</span></span> <span data-ttu-id="bd1af-177">描述此最佳的辦法是 whith 具體的範例：</span><span class="sxs-lookup"><span data-stu-id="bd1af-177">The best way to describe this is whith a concrete example:</span></span>

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

<span data-ttu-id="bd1af-178">預設情況下同時使用 employee 和 manager 會對應至資料庫中相同的資料表 （員工）。</span><span class="sxs-lookup"><span data-stu-id="bd1af-178">By default both employee and manager are mapped to the same table (Employees) in the database.</span></span> <span data-ttu-id="bd1af-179">資料表包含員工和經理會告訴您何種類型的執行個體儲存在每個資料列中的鑑別子資料行。</span><span class="sxs-lookup"><span data-stu-id="bd1af-179">The table will contain both employees and managers with a discriminator column that will tell you what type of instance is stored in each row.</span></span> <span data-ttu-id="bd1af-180">這會是 TPH 對應，因為沒有單一階層的資料表。</span><span class="sxs-lookup"><span data-stu-id="bd1af-180">This is TPH mapping as there is a single table for the hierarchy.</span></span> <span data-ttu-id="bd1af-181">不過，如果您在這兩個 classe 呼叫 ToTable 然後每個型別會改為對應至其本身的資料表，也稱為 TPT 因為每個類型都有自己的資料表。</span><span class="sxs-lookup"><span data-stu-id="bd1af-181">However, if you call ToTable on both classe then each type will instead be mapped to its own table, also known as TPT since each type has its own table.</span></span>

``` csharp
    modelBuilder.Types()
                .Configure(c=>c.ToTable(c.ClrType.Name));
```

<span data-ttu-id="bd1af-182">上述程式碼會對應至資料表結構看起來如下所示：</span><span class="sxs-lookup"><span data-stu-id="bd1af-182">The code above will map to a table structure that looks like the following:</span></span>

![tpt 範例](~/ef6/media/tptexample.jpg)

<span data-ttu-id="bd1af-184">您可以避免這項目，並維護預設 TPH 對應，透過幾種方式：</span><span class="sxs-lookup"><span data-stu-id="bd1af-184">You can avoid this, and maintain the default TPH mapping, in a couple ways:</span></span>

1.  <span data-ttu-id="bd1af-185">呼叫 ToTable 階層中每個類型相同的資料表名稱。</span><span class="sxs-lookup"><span data-stu-id="bd1af-185">Call ToTable with the same table name for each type in the hierarchy.</span></span>
2.  <span data-ttu-id="bd1af-186">只在階層中，在我們的範例是員工的基底類別上呼叫 ToTable。</span><span class="sxs-lookup"><span data-stu-id="bd1af-186">Call ToTable only on the base class of the hierarchy, in our example that would be employee.</span></span>

 

## <a name="execution-order"></a><span data-ttu-id="bd1af-187">執行順序</span><span class="sxs-lookup"><span data-stu-id="bd1af-187">Execution Order</span></span>

<span data-ttu-id="bd1af-188">慣例以最後一個 wins 方式運作，Fluent API 相同。</span><span class="sxs-lookup"><span data-stu-id="bd1af-188">Conventions operate in a last wins manner, the same as the Fluent API.</span></span> <span data-ttu-id="bd1af-189">這表示，如果您要撰寫兩個設定相同屬性的相同選項的慣例，則最後一個執行 wins。</span><span class="sxs-lookup"><span data-stu-id="bd1af-189">What this means is that if you write two conventions that configure the same option of the same property, then the last one to execute wins.</span></span> <span data-ttu-id="bd1af-190">舉例來說，下列程式碼中的所有字串的最大長度設為 500，但接著就設定在模型中才能具有最大長度 250 名為 Name 的所有屬性。</span><span class="sxs-lookup"><span data-stu-id="bd1af-190">As an example, in the code below the max length of all strings is set to 500 but we then configure all properties called Name in the model to have a max length of 250.</span></span>

``` csharp
    modelBuilder.Properties<string>()
                .Configure(c => c.HasMaxLength(500));

    modelBuilder.Properties<string>()
                .Where(x => x.Name == "Name")
                .Configure(c => c.HasMaxLength(250));
```

<span data-ttu-id="bd1af-191">要設定長度上限為 250 的慣例後的所有字串都設定為 500，因為在我們的模型稱為名稱的所有屬性會有長度上限為 250 任何其他字串，例如描述時，會是 500。</span><span class="sxs-lookup"><span data-stu-id="bd1af-191">Because the convention to set max length to 250 is after the one that sets all strings to 500, all the properties called Name in our model will have a MaxLength of 250 while any other strings, such as descriptions, would be 500.</span></span> <span data-ttu-id="bd1af-192">使用慣例，如此一來表示，您可以提供一般慣例類型或內容中您模型，然後覆寫它們以提供不同的子集。</span><span class="sxs-lookup"><span data-stu-id="bd1af-192">Using conventions in this way means that you can provide a general convention for types or properties in your model and then overide them for subsets that are different.</span></span>

<span data-ttu-id="bd1af-193">資料註解與 Fluent API 也可用來覆寫慣例，以在特定情況下。</span><span class="sxs-lookup"><span data-stu-id="bd1af-193">The Fluent API and Data Annotations can also be used to override a convention in specific cases.</span></span> <span data-ttu-id="bd1af-194">在上面的範例如果我們使用 Fluent API 來設定屬性的最大長度然後我們可能已將它放之前或之後的慣例，因為更特定的 Fluent API，將會贏得更一般的設定慣例。</span><span class="sxs-lookup"><span data-stu-id="bd1af-194">In our example above if we had used the Fluent API to set the max length of a property then we could have put it before or after the convention, because the more specific Fluent API will win over the more general Configuration Convention.</span></span>

 

## <a name="built-in-conventions"></a><span data-ttu-id="bd1af-195">內建慣例</span><span class="sxs-lookup"><span data-stu-id="bd1af-195">Built-in Conventions</span></span>

<span data-ttu-id="bd1af-196">自訂慣例可能會受到預設 Code First 慣例，因為它可用來將慣例新增到執行之前或之後另一個的慣例。</span><span class="sxs-lookup"><span data-stu-id="bd1af-196">Because custom conventions could be affected by the default Code First conventions, it can be useful to add conventions to run before or after another convention.</span></span> <span data-ttu-id="bd1af-197">若要這樣做中，您可以使用慣例集合 AddBefore 和 AddAfter 的方法在您的衍生 DbContext 上。</span><span class="sxs-lookup"><span data-stu-id="bd1af-197">To do this you can use the AddBefore and AddAfter methods of the Conventions collection on your derived DbContext.</span></span> <span data-ttu-id="bd1af-198">下列程式碼會新增我們稍早建立，讓它將會執行之前的內建慣例類別中索引鍵探索慣例。</span><span class="sxs-lookup"><span data-stu-id="bd1af-198">The following code would add the convention class we created earlier so that it will run before the built in key discovery convention.</span></span>

``` csharp
    modelBuilder.Conventions.AddBefore<IdKeyDiscoveryConvention>(new DateTime2Convention());
```

<span data-ttu-id="bd1af-199">這將會充分使用的，新增需要執行之前或之後的內建慣例的慣例時，一份內建的慣例可以在這裡找到： [System.Data.Entity.ModelConfiguration.Conventions 命名空間](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx).</span><span class="sxs-lookup"><span data-stu-id="bd1af-199">This is going to be of the most use when adding conventions that need to run before or after the built in conventions, a list of the built in conventions can be found here: [System.Data.Entity.ModelConfiguration.Conventions Namespace](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx).</span></span>

<span data-ttu-id="bd1af-200">您也可以移除您不想套用至您的模型的慣例。</span><span class="sxs-lookup"><span data-stu-id="bd1af-200">You can also remove conventions that you do not want applied to your model.</span></span> <span data-ttu-id="bd1af-201">若要移除的慣例，請使用 Remove 方法。</span><span class="sxs-lookup"><span data-stu-id="bd1af-201">To remove a convention, use the Remove method.</span></span> <span data-ttu-id="bd1af-202">以下是移除 PluralizingTableNameConvention 的範例。</span><span class="sxs-lookup"><span data-stu-id="bd1af-202">Here is an example of removing the PluralizingTableNameConvention.</span></span>

``` csharp
    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Conventions.Remove<PluralizingTableNameConvention>();
    }
```
