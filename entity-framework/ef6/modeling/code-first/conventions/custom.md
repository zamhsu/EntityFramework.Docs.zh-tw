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
# <a name="custom-code-first-conventions"></a><span data-ttu-id="cd7ce-102">自訂 Code First 慣例</span><span class="sxs-lookup"><span data-stu-id="cd7ce-102">Custom Code First Conventions</span></span>
> [!NOTE]
> <span data-ttu-id="cd7ce-103">**僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="cd7ce-104">如果您使用的是較早版本，則不適用部分或全部的資訊。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-104">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="cd7ce-105">使用 Code First 您的模型是使用一組慣例從類別計算而來。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-105">When using Code First your model is calculated from your classes using a set of conventions.</span></span> <span data-ttu-id="cd7ce-106">預設[Code First 慣例](~/ef6/modeling/code-first/conventions/built-in.md)會決定哪些屬性會成為實體的主要金鑰、實體所對應的資料表名稱，以及十進位資料行預設具有的精確度和小數位數。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-106">The default [Code First Conventions](~/ef6/modeling/code-first/conventions/built-in.md) determine things like which property becomes the primary key of an entity, the name of the table an entity maps to, and what precision and scale a decimal column has by default.</span></span>

<span data-ttu-id="cd7ce-107">有時候，這些預設慣例並不適合您的模型，而且您必須使用資料批註或流暢的 API 來設定許多個別實體來解決這些問題。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-107">Sometimes these default conventions are not ideal for your model, and you have to work around them by configuring many individual entities using Data Annotations or the Fluent API.</span></span> <span data-ttu-id="cd7ce-108">自訂 Code First 慣例可讓您定義自己的慣例，為您的模型提供設定預設值。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-108">Custom Code First Conventions let you define your own conventions that provide configuration defaults for your model.</span></span> <span data-ttu-id="cd7ce-109">在本逐步解說中，我們將探討不同類型的自訂慣例，以及如何建立它們。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-109">In this walkthrough, we will explore the different types of custom conventions and how to create each of them.</span></span>


## <a name="model-based-conventions"></a><span data-ttu-id="cd7ce-110">以模型為基礎的慣例</span><span class="sxs-lookup"><span data-stu-id="cd7ce-110">Model-Based Conventions</span></span>

<span data-ttu-id="cd7ce-111">本頁面涵蓋適用于自訂慣例的 DbModelBuilder API。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-111">This page covers the DbModelBuilder API for custom conventions.</span></span> <span data-ttu-id="cd7ce-112">此 API 應該足以撰寫大部分的自訂慣例。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-112">This API should be sufficient for authoring most custom conventions.</span></span> <span data-ttu-id="cd7ce-113">不過，也能夠撰寫以模型為基礎的慣例-在建立後操作最終模型的慣例-以處理 advanced 案例。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-113">However, there is also the ability to author model-based conventions - conventions that manipulate the final model once it is created - to handle advanced scenarios.</span></span> <span data-ttu-id="cd7ce-114">如需詳細資訊，請參閱以[模型為基礎的慣例](~/ef6/modeling/code-first/conventions/model.md)。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-114">For more information, see [Model-Based Conventions](~/ef6/modeling/code-first/conventions/model.md).</span></span>

 

## <a name="our-model"></a><span data-ttu-id="cd7ce-115">我們的模型</span><span class="sxs-lookup"><span data-stu-id="cd7ce-115">Our Model</span></span>

<span data-ttu-id="cd7ce-116">讓我們從定義可用於慣例的簡單模型開始。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-116">Let's start by defining a simple model that we can use with our conventions.</span></span> <span data-ttu-id="cd7ce-117">將下列類別新增至您的專案。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-117">Add the following classes to your project.</span></span>

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

 

## <a name="introducing-custom-conventions"></a><span data-ttu-id="cd7ce-118">自訂慣例簡介</span><span class="sxs-lookup"><span data-stu-id="cd7ce-118">Introducing Custom Conventions</span></span>

<span data-ttu-id="cd7ce-119">讓我們撰寫一個慣例，將任何名為 Key 的屬性設定為其實體類型的主要金鑰。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-119">Let’s write a convention that configures any property named Key to be the primary key for its entity type.</span></span>

<span data-ttu-id="cd7ce-120">在模型產生器上啟用慣例，可以藉由覆寫內容中的 OnModelCreating 來存取。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-120">Conventions are enabled on the model builder, which can be accessed by overriding OnModelCreating in the context.</span></span> <span data-ttu-id="cd7ce-121">更新 ProductCoNtext 類別，如下所示：</span><span class="sxs-lookup"><span data-stu-id="cd7ce-121">Update the ProductContext class as follows:</span></span>

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

<span data-ttu-id="cd7ce-122">現在，名為 Key 之模型中的任何屬性，都會設定為其元件所屬實體的主要索引鍵。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-122">Now, any property in our model named Key will be configured as the primary key of whatever entity its part of.</span></span>

<span data-ttu-id="cd7ce-123">我們也可以篩選我們要設定的屬性類型，讓我們的慣例更為明確：</span><span class="sxs-lookup"><span data-stu-id="cd7ce-123">We could also make our conventions more specific by filtering on the type of property that we are going to configure:</span></span>

``` csharp
    modelBuilder.Properties<int>()
                .Where(p => p.Name == "Key")
                .Configure(p => p.IsKey());
```

<span data-ttu-id="cd7ce-124">這會將名為 Key 的所有屬性設定為其實體的主要金鑰，但僅限於其為整數。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-124">This will configure all properties called Key to be the primary key of their entity, but only if they are an integer.</span></span>

<span data-ttu-id="cd7ce-125">IsKey 方法有一個有趣的功能，就是它是累加的。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-125">An interesting feature of the IsKey method is that it is additive.</span></span> <span data-ttu-id="cd7ce-126">這表示如果您在多個屬性上呼叫 IsKey，它們全都會成為複合索引鍵的一部分。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-126">Which means that if you call IsKey on multiple properties and they will all become part of a composite key.</span></span> <span data-ttu-id="cd7ce-127">要注意的是，當您為索引鍵指定多個屬性時，也必須指定這些屬性的順序。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-127">The one caveat for this is that when you specify multiple properties for a key you must also specify an order for those properties.</span></span> <span data-ttu-id="cd7ce-128">若要這麼做，您可以呼叫 HasColumnOrder 方法，如下所示：</span><span class="sxs-lookup"><span data-stu-id="cd7ce-128">You can do this by calling the HasColumnOrder method like below:</span></span>

``` csharp
    modelBuilder.Properties<int>()
                .Where(x => x.Name == "Key")
                .Configure(x => x.IsKey().HasColumnOrder(1));

    modelBuilder.Properties()
                .Where(x => x.Name == "Name")
                .Configure(x => x.IsKey().HasColumnOrder(2));
```

<span data-ttu-id="cd7ce-129">此程式碼會設定模型中的類型，使其具有由 int 索引鍵資料行和字串名稱資料行組成的複合索引鍵。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-129">This code will configure the types in our model to have a composite key consisting of the int Key column and the string Name column.</span></span> <span data-ttu-id="cd7ce-130">如果我們在設計工具中看到模型，它看起來會像這樣：</span><span class="sxs-lookup"><span data-stu-id="cd7ce-130">If we view the model in the designer it would look like this:</span></span>

![複合索引鍵](~/ef6/media/compositekey.png)

<span data-ttu-id="cd7ce-132">屬性慣例的另一個範例是設定我的模型中的所有日期時間屬性，以對應至 SQL Server 中的 datetime2 型別，而不是 DateTime。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-132">Another example of property conventions is to configure all DateTime properties in my model to map to the datetime2 type in SQL Server instead of datetime.</span></span> <span data-ttu-id="cd7ce-133">您可以使用下列各項來達到此目的：</span><span class="sxs-lookup"><span data-stu-id="cd7ce-133">You can achieve this with the following:</span></span>

``` csharp
    modelBuilder.Properties<DateTime>()
                .Configure(c => c.HasColumnType("datetime2"));
```

 

## <a name="convention-classes"></a><span data-ttu-id="cd7ce-134">慣例類別</span><span class="sxs-lookup"><span data-stu-id="cd7ce-134">Convention Classes</span></span>

<span data-ttu-id="cd7ce-135">定義慣例的另一種方式是使用慣例類別來封裝您的慣例。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-135">Another way of defining conventions is to use a Convention Class to encapsulate your convention.</span></span> <span data-ttu-id="cd7ce-136">當使用慣例類別時，您會建立繼承自 ModelConfiguration 的慣例類別的型別。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-136">When using a Convention Class then you create a type that inherits from the Convention class in the System.Data.Entity.ModelConfiguration.Conventions namespace.</span></span>

<span data-ttu-id="cd7ce-137">我們可以藉由執行下列動作，以先前所示的 datetime2 慣例來建立慣例類別：</span><span class="sxs-lookup"><span data-stu-id="cd7ce-137">We can create a Convention Class with the datetime2 convention that we showed earlier by doing the following:</span></span>

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

<span data-ttu-id="cd7ce-138">若要告訴 EF 使用此慣例，您可以將它新增至 OnModelCreating 中的慣例集合，如果您已經遵循此逐步解說，就會如下所示：</span><span class="sxs-lookup"><span data-stu-id="cd7ce-138">To tell EF to use this convention you add it to the Conventions collection in OnModelCreating, which if you’ve been following along with the walkthrough will look like this:</span></span>

``` csharp
    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Properties<int>()
                    .Where(p => p.Name.EndsWith("Key"))
                    .Configure(p => p.IsKey());

        modelBuilder.Conventions.Add(new DateTime2Convention());
    }
```

<span data-ttu-id="cd7ce-139">如您所見，我們會將慣例的實例新增至慣例集合。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-139">As you can see we add an instance of our convention to the conventions collection.</span></span> <span data-ttu-id="cd7ce-140">繼承自慣例可提供便利的方式來分組和共用小組或專案之間的慣例。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-140">Inheriting from Convention provides a convenient way of grouping and sharing conventions across teams or projects.</span></span> <span data-ttu-id="cd7ce-141">例如，您可以有一個類別庫，其中包含一組通用慣例，讓您的所有組織專案都使用。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-141">You could, for example, have a class library with a common set of conventions that all of your organizations projects use.</span></span>

 

## <a name="custom-attributes"></a><span data-ttu-id="cd7ce-142">自訂屬性</span><span class="sxs-lookup"><span data-stu-id="cd7ce-142">Custom Attributes</span></span>

<span data-ttu-id="cd7ce-143">另一個很棒的慣例使用，就是要在設定模型時，使用新的屬性。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-143">Another great use of conventions is to enable new attributes to be used when configuring a model.</span></span> <span data-ttu-id="cd7ce-144">為了說明這一點，讓我們建立一個可以用來將字串屬性標示為非 Unicode 的屬性。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-144">To illustrate this, let’s create an attribute that we can use to mark String properties as non-Unicode.</span></span>

``` csharp
    [AttributeUsage(AttributeTargets.Property, AllowMultiple = false)]
    public class NonUnicode : Attribute
    {
    }
```

<span data-ttu-id="cd7ce-145">現在，讓我們建立一個慣例，將此屬性套用至我們的模型：</span><span class="sxs-lookup"><span data-stu-id="cd7ce-145">Now, let’s create a convention to apply this attribute to our model:</span></span>

``` csharp
    modelBuilder.Properties()
                .Where(x => x.GetCustomAttributes(false).OfType<NonUnicode>().Any())
                .Configure(c => c.IsUnicode(false));
```

<span data-ttu-id="cd7ce-146">在此慣例中，我們可以將 NonUnicode 屬性加入至我們的任何字串屬性，這表示資料庫中的資料行將會儲存為 Varchar 而非 Nvarchar。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-146">With this convention we can add the NonUnicode attribute to any of our string properties, which means the column in the database will be stored as varchar instead of nvarchar.</span></span>

<span data-ttu-id="cd7ce-147">關於此慣例，有一點要注意的是，如果您將 NonUnicode 屬性放在字串屬性以外的任何地方，則會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-147">One thing to note about this convention is that if you put the NonUnicode attribute on anything other than a string property then it will throw an exception.</span></span> <span data-ttu-id="cd7ce-148">這是因為您無法在字串以外的任何類型上設定 IsUnicode。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-148">It does this because you cannot configure IsUnicode on any type other than a string.</span></span> <span data-ttu-id="cd7ce-149">如果發生這種情況，您可以讓慣例更明確，讓它篩選掉不是字串的任何專案。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-149">If this happens, then you can make your convention more specific, so that it filters out anything that isn’t a string.</span></span>

<span data-ttu-id="cd7ce-150">雖然上述慣例適用于定義自訂屬性，但還有另一個 API 可以更容易使用，特別是當您想要使用屬性類別中的屬性時。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-150">While the above convention works for defining custom attributes there is another API that can be much easier to use, especially when you want to use properties from the attribute class.</span></span>

<span data-ttu-id="cd7ce-151">在此範例中，我們將更新屬性，並將其變更為 IsUnicode 屬性，讓它看起來像這樣：</span><span class="sxs-lookup"><span data-stu-id="cd7ce-151">For this example we are going to update our attribute and change it to an IsUnicode attribute, so it looks like this:</span></span>

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

<span data-ttu-id="cd7ce-152">這麼做之後，我們可以在屬性上設定 bool，以告知慣例屬性是否應為 Unicode。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-152">Once we have this, we can set a bool on our attribute to tell the convention whether or not a property should be Unicode.</span></span> <span data-ttu-id="cd7ce-153">我們可以藉由存取設定類別的 ClrProperty 來執行這項操作，如下所示：</span><span class="sxs-lookup"><span data-stu-id="cd7ce-153">We could do this in the convention we have already by accessing the ClrProperty of the configuration class like this:</span></span>

``` csharp
    modelBuilder.Properties()
                .Where(x => x.GetCustomAttributes(false).OfType<IsUnicode>().Any())
                .Configure(c => c.IsUnicode(c.ClrPropertyInfo.GetCustomAttribute<IsUnicode>().Unicode));
```

<span data-ttu-id="cd7ce-154">這很簡單，但使用慣例 API 的 Having 方法，可以更簡潔的方式達到此目的。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-154">This is easy enough, but there is a more succinct way of achieving this by using the Having method of the conventions API.</span></span> <span data-ttu-id="cd7ce-155">Having 方法具有 Func 類型的參數&lt;PropertyInfo，T&gt; 會接受與 Where 方法相同的 PropertyInfo，但預期會傳回物件。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-155">The Having method has a parameter of type Func&lt;PropertyInfo, T&gt; which accepts the PropertyInfo the same as the Where method, but is expected to return an object.</span></span> <span data-ttu-id="cd7ce-156">如果傳回的物件是 null，則不會設定屬性，這表示您可以用它來篩選屬性，就像在其中一樣，但它也會捕捉傳回的物件，並將它傳遞給 Configure 方法。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-156">If the returned object is null then the property will not be configured, which means you can filter out properties with it just like Where, but it is different in that it will also capture the returned object and pass it to the Configure method.</span></span> <span data-ttu-id="cd7ce-157">其運作方式如下所示：</span><span class="sxs-lookup"><span data-stu-id="cd7ce-157">This works like the following:</span></span>

``` csharp
    modelBuilder.Properties()
                .Having(x =>x.GetCustomAttributes(false).OfType<IsUnicode>().FirstOrDefault())
                .Configure((config, att) => config.IsUnicode(att.Unicode));
```

<span data-ttu-id="cd7ce-158">自訂屬性不是唯一使用 Having 方法的理由，在設定型別或屬性時，您需要進行篩選的任何地方都很有用。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-158">Custom attributes are not the only reason to use the Having method, it is useful anywhere that you need to reason about something that you are filtering on when configuring your types or properties.</span></span>

 

## <a name="configuring-types"></a><span data-ttu-id="cd7ce-159">設定類型</span><span class="sxs-lookup"><span data-stu-id="cd7ce-159">Configuring Types</span></span>

<span data-ttu-id="cd7ce-160">到目前為止，我們所有的慣例都適用于屬性，但在您的模型中設定類型的慣例 API 有另一個區域。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-160">So far all of our conventions have been for properties, but there is another area of the conventions API for configuring the types in your model.</span></span> <span data-ttu-id="cd7ce-161">此體驗與我們目前所見的慣例類似，但 [設定] 內的選項會位於實體而非屬性層級。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-161">The experience is similar to the conventions we have seen so far, but the options inside configure will be at the entity instead of property level.</span></span>

<span data-ttu-id="cd7ce-162">類型層級慣例的其中一項功能非常適合用來變更資料表命名慣例，也就是對應到與 EF 預設值不同的現有架構，或者使用不同的命名慣例來建立新的資料庫。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-162">One of the things that Type level conventions can be really useful for is changing the table naming convention, either to map to an existing schema that differs from the EF default or to create a new database with a different naming convention.</span></span> <span data-ttu-id="cd7ce-163">若要這麼做，我們必須先有方法可以接受模型中類型的 TypeInfo，並傳回該類型的資料表名稱應為：</span><span class="sxs-lookup"><span data-stu-id="cd7ce-163">To do this we first need a method that can accept the TypeInfo for a type in our model and return what the table name for that type should be:</span></span>

``` csharp
    private string GetTableName(Type type)
    {
        var result = Regex.Replace(type.Name, ".[A-Z]", m => m.Value[0] + "_" + m.Value[1]);

        return result.ToLower();
    }
```

<span data-ttu-id="cd7ce-164">這個方法會採用類型，並傳回使用小寫加底線的字串，而不是 CamelCase。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-164">This method takes a type and returns a string that uses lower case with underscores instead of CamelCase.</span></span> <span data-ttu-id="cd7ce-165">在我們的模型中，這表示 ProductCategory 類別會對應至名為 product\_category 的資料表，而不是 ProductCategories。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-165">In our model this means that the ProductCategory class will be mapped to a table called product\_category instead of ProductCategories.</span></span>

<span data-ttu-id="cd7ce-166">有了這個方法之後，我們就可以像這樣的慣例來呼叫它：</span><span class="sxs-lookup"><span data-stu-id="cd7ce-166">Once we have that method we can call it in a convention like this:</span></span>

``` csharp
    modelBuilder.Types()
                .Configure(c => c.ToTable(GetTableName(c.ClrType)));
```

<span data-ttu-id="cd7ce-167">此慣例會將模型中的每個類型設定為對應至 GetTableName 方法所傳回的資料表名稱。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-167">This convention configures every type in our model to map to the table name that is returned from our GetTableName method.</span></span> <span data-ttu-id="cd7ce-168">此慣例等同于使用流暢的 API 針對模型中的每個實體呼叫 ToTable 方法。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-168">This convention is the equivalent to calling the ToTable method for each entity in the model using the Fluent API.</span></span>

<span data-ttu-id="cd7ce-169">有一點要注意的是，當您呼叫 ToTable 時，EF 會採用您提供的字串做為確切的資料表名稱，而不需要在決定資料表名稱時，通常會執行的任何複數表示。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-169">One thing to note about this is that when you call ToTable EF will take the string that you provide as the exact table name, without any of the pluralization that it would normally do when determining table names.</span></span> <span data-ttu-id="cd7ce-170">這就是為什麼我們慣例的資料表名稱是產品\_的類別，而不是產品\_的類別。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-170">This is why the table name from our convention is product\_category instead of product\_categories.</span></span> <span data-ttu-id="cd7ce-171">我們可以自行呼叫複數表示服務，以在我們的慣例中解決此問題。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-171">We can resolve that in our convention by making a call to the pluralization service ourselves.</span></span>

<span data-ttu-id="cd7ce-172">在下列程式碼中，我們將使用 EF6 中新增的相依性[解析](~/ef6/fundamentals/configuring/dependency-resolution.md)功能來抓取 EF 已使用的複數表示服務，並將複數化我們的資料表名稱。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-172">In the following code we will use the [Dependency Resolution](~/ef6/fundamentals/configuring/dependency-resolution.md) feature added in EF6 to retrieve the pluralization service that EF would have used and pluralize our table name.</span></span>

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
> <span data-ttu-id="cd7ce-173">GetService 的泛型版本是 DependencyResolution 命名空間中的擴充方法，您必須將 using 語句加入至您的內容，才能使用它。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-173">The generic version of GetService is an extension method in the System.Data.Entity.Infrastructure.DependencyResolution namespace, you will need to add a using statement to your context in order to use it.</span></span>

### <a name="totable-and-inheritance"></a><span data-ttu-id="cd7ce-174">ToTable 和繼承</span><span class="sxs-lookup"><span data-stu-id="cd7ce-174">ToTable and Inheritance</span></span>

<span data-ttu-id="cd7ce-175">ToTable 的另一個重要層面是，如果您將類型明確對應至指定的資料表，則可以改變 EF 將使用的對應策略。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-175">Another important aspect of ToTable is that if you explicitly map a type to a given table, then you can alter the mapping strategy that EF will use.</span></span> <span data-ttu-id="cd7ce-176">如果您針對繼承階層架構中的每個類型呼叫 ToTable，傳遞類型名稱做為上述的資料表名稱，則您會將預設的每一階層資料表（TPH）對應策略變更為每一類型的資料表（TPT）。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-176">If you call ToTable for every type in an inheritance hierarchy, passing the type name as the name of the table like we did above, then you will change the default Table-Per-Hierarchy (TPH) mapping strategy to Table-Per-Type (TPT).</span></span> <span data-ttu-id="cd7ce-177">描述這一點的最佳方式是 whith 具體的範例：</span><span class="sxs-lookup"><span data-stu-id="cd7ce-177">The best way to describe this is whith a concrete example:</span></span>

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

<span data-ttu-id="cd7ce-178">根據預設，employee 和 manager 都會對應到資料庫中的相同資料表（員工）。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-178">By default both employee and manager are mapped to the same table (Employees) in the database.</span></span> <span data-ttu-id="cd7ce-179">資料表將同時包含員工和經理與鑒別子資料行，以告訴您每個資料列中儲存了哪種類型的實例。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-179">The table will contain both employees and managers with a discriminator column that will tell you what type of instance is stored in each row.</span></span> <span data-ttu-id="cd7ce-180">這是 TPH 對應，因為階層有單一資料表。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-180">This is TPH mapping as there is a single table for the hierarchy.</span></span> <span data-ttu-id="cd7ce-181">不過，如果您在這兩個 classe 上呼叫 ToTable，則每個類型都會對應到它自己的資料表（也稱為 TPT），因為每個類型都有自己的資料表。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-181">However, if you call ToTable on both classe then each type will instead be mapped to its own table, also known as TPT since each type has its own table.</span></span>

``` csharp
    modelBuilder.Types()
                .Configure(c=>c.ToTable(c.ClrType.Name));
```

<span data-ttu-id="cd7ce-182">上述程式碼會對應至如下所示的資料表結構：</span><span class="sxs-lookup"><span data-stu-id="cd7ce-182">The code above will map to a table structure that looks like the following:</span></span>

![tpt 範例](~/ef6/media/tptexample.jpg)

<span data-ttu-id="cd7ce-184">您可以透過幾種方式來避免這種情況，並維護預設的 TPH 對應：</span><span class="sxs-lookup"><span data-stu-id="cd7ce-184">You can avoid this, and maintain the default TPH mapping, in a couple ways:</span></span>

1.  <span data-ttu-id="cd7ce-185">針對階層中的每個類型，使用相同的資料表名稱來呼叫 ToTable。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-185">Call ToTable with the same table name for each type in the hierarchy.</span></span>
2.  <span data-ttu-id="cd7ce-186">只在階層的基類（在我們的範例中為 employee）上呼叫 ToTable。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-186">Call ToTable only on the base class of the hierarchy, in our example that would be employee.</span></span>

 

## <a name="execution-order"></a><span data-ttu-id="cd7ce-187">執行順序</span><span class="sxs-lookup"><span data-stu-id="cd7ce-187">Execution Order</span></span>

<span data-ttu-id="cd7ce-188">慣例會以最後的 wins 方式運作，與流暢的 API 相同。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-188">Conventions operate in a last wins manner, the same as the Fluent API.</span></span> <span data-ttu-id="cd7ce-189">這表示如果您撰寫兩個慣例來設定相同屬性的相同選項，則最後一個會執行 wins。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-189">What this means is that if you write two conventions that configure the same option of the same property, then the last one to execute wins.</span></span> <span data-ttu-id="cd7ce-190">例如，在下列程式碼中，所有字串的最大長度都設為500，但我們接著將模型中所有名為 Name 的屬性設定為最大長度250。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-190">As an example, in the code below the max length of all strings is set to 500 but we then configure all properties called Name in the model to have a max length of 250.</span></span>

``` csharp
    modelBuilder.Properties<string>()
                .Configure(c => c.HasMaxLength(500));

    modelBuilder.Properties<string>()
                .Where(x => x.Name == "Name")
                .Configure(c => c.HasMaxLength(250));
```

<span data-ttu-id="cd7ce-191">因為將最大長度設定為250的慣例是在將所有字串設定為500的情況下，所以模型中所有名為 Name 的屬性都會有一個250的 MaxLength，而任何其他字串（例如描述）都會是500。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-191">Because the convention to set max length to 250 is after the one that sets all strings to 500, all the properties called Name in our model will have a MaxLength of 250 while any other strings, such as descriptions, would be 500.</span></span> <span data-ttu-id="cd7ce-192">以這種方式使用慣例，表示您可以在模型中提供類型或屬性的一般慣例，然後針對不同的子集加以覆寫。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-192">Using conventions in this way means that you can provide a general convention for types or properties in your model and then overide them for subsets that are different.</span></span>

<span data-ttu-id="cd7ce-193">流暢的 API 和資料批註也可以用來覆寫特定案例中的慣例。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-193">The Fluent API and Data Annotations can also be used to override a convention in specific cases.</span></span> <span data-ttu-id="cd7ce-194">在上述範例中，如果我們使用了流暢的 API 來設定屬性的最大長度，則我們可以將它放在慣例之前或之後，因為更具體的流暢 API 將會優先于較一般的設定慣例。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-194">In our example above if we had used the Fluent API to set the max length of a property then we could have put it before or after the convention, because the more specific Fluent API will win over the more general Configuration Convention.</span></span>

 

## <a name="built-in-conventions"></a><span data-ttu-id="cd7ce-195">內建慣例</span><span class="sxs-lookup"><span data-stu-id="cd7ce-195">Built-in Conventions</span></span>

<span data-ttu-id="cd7ce-196">因為自訂慣例可能會受到預設 Code First 慣例的影響，所以新增慣例以在另一個慣例之前或之後執行可能會很有用。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-196">Because custom conventions could be affected by the default Code First conventions, it can be useful to add conventions to run before or after another convention.</span></span> <span data-ttu-id="cd7ce-197">若要這麼做，您可以在衍生的 DbCoNtext 上使用慣例集合的 AddBefore 和 AddAfter 方法。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-197">To do this you can use the AddBefore and AddAfter methods of the Conventions collection on your derived DbContext.</span></span> <span data-ttu-id="cd7ce-198">下列程式碼會新增我們稍早建立的慣例類別，讓它能夠在內建的金鑰探索慣例之前執行。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-198">The following code would add the convention class we created earlier so that it will run before the built in key discovery convention.</span></span>

``` csharp
    modelBuilder.Conventions.AddBefore<IdKeyDiscoveryConvention>(new DateTime2Convention());
```

<span data-ttu-id="cd7ce-199">在新增必須在內建慣例之前或之後執行的慣例時，這會是最常使用的，您可以在這裡找到內建慣例清單： [ModelConfiguration. 慣例 Namespace](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx)。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-199">This is going to be of the most use when adding conventions that need to run before or after the built in conventions, a list of the built in conventions can be found here: [System.Data.Entity.ModelConfiguration.Conventions Namespace](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx).</span></span>

<span data-ttu-id="cd7ce-200">您也可以移除您不想要套用至模型的慣例。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-200">You can also remove conventions that you do not want applied to your model.</span></span> <span data-ttu-id="cd7ce-201">若要移除慣例，請使用 Remove 方法。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-201">To remove a convention, use the Remove method.</span></span> <span data-ttu-id="cd7ce-202">以下是移除 PluralizingTableNameConvention 的範例。</span><span class="sxs-lookup"><span data-stu-id="cd7ce-202">Here is an example of removing the PluralizingTableNameConvention.</span></span>

``` csharp
    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Conventions.Remove<PluralizingTableNameConvention>();
    }
```
