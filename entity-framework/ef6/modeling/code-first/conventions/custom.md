---
title: 自訂 Code First 慣例-EF6
description: Entity Framework 6 中的自訂 Code First 慣例
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/code-first/conventions/custom
ms.openlocfilehash: 19f22d311438b8c7d3ac9d270017f3ea4907efda
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90074025"
---
# <a name="custom-code-first-conventions"></a><span data-ttu-id="ce404-103">自訂 Code First 慣例</span><span class="sxs-lookup"><span data-stu-id="ce404-103">Custom Code First Conventions</span></span>
> [!NOTE]
> <span data-ttu-id="ce404-104">**僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。</span><span class="sxs-lookup"><span data-stu-id="ce404-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="ce404-105">如果您使用的是較早版本，則不適用部分或全部的資訊。</span><span class="sxs-lookup"><span data-stu-id="ce404-105">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="ce404-106">使用 Code First 您的模型會使用一組慣例從您的類別計算。</span><span class="sxs-lookup"><span data-stu-id="ce404-106">When using Code First your model is calculated from your classes using a set of conventions.</span></span> <span data-ttu-id="ce404-107">預設的 [Code First 慣例](xref:ef6/modeling/code-first/conventions/built-in) 會決定哪些屬性會成為實體的主鍵、實體所對應的資料表名稱，以及 decimal 資料行預設的精確度和小數位數。</span><span class="sxs-lookup"><span data-stu-id="ce404-107">The default [Code First Conventions](xref:ef6/modeling/code-first/conventions/built-in) determine things like which property becomes the primary key of an entity, the name of the table an entity maps to, and what precision and scale a decimal column has by default.</span></span>

<span data-ttu-id="ce404-108">這些預設慣例有時不適合您的模型，而且您必須使用資料批註或流暢的 API 來設定許多個別實體來解決這些預設慣例。</span><span class="sxs-lookup"><span data-stu-id="ce404-108">Sometimes these default conventions are not ideal for your model, and you have to work around them by configuring many individual entities using Data Annotations or the Fluent API.</span></span> <span data-ttu-id="ce404-109">自訂 Code First 慣例可讓您定義自己的慣例，以提供模型的設定預設值。</span><span class="sxs-lookup"><span data-stu-id="ce404-109">Custom Code First Conventions let you define your own conventions that provide configuration defaults for your model.</span></span> <span data-ttu-id="ce404-110">在這個逐步解說中，我們將探討不同類型的自訂慣例，以及如何建立每個類型。</span><span class="sxs-lookup"><span data-stu-id="ce404-110">In this walkthrough, we will explore the different types of custom conventions and how to create each of them.</span></span>


## <a name="model-based-conventions"></a><span data-ttu-id="ce404-111">以模型為基礎的慣例</span><span class="sxs-lookup"><span data-stu-id="ce404-111">Model-Based Conventions</span></span>

<span data-ttu-id="ce404-112">本頁面涵蓋自訂慣例的 DbModelBuilder API。</span><span class="sxs-lookup"><span data-stu-id="ce404-112">This page covers the DbModelBuilder API for custom conventions.</span></span> <span data-ttu-id="ce404-113">此 API 應該足以撰寫大部分的自訂慣例。</span><span class="sxs-lookup"><span data-stu-id="ce404-113">This API should be sufficient for authoring most custom conventions.</span></span> <span data-ttu-id="ce404-114">不過，您也可以撰寫以模型為基礎的慣例慣例，在建立模型時操作最終模型，以處理先進的案例。</span><span class="sxs-lookup"><span data-stu-id="ce404-114">However, there is also the ability to author model-based conventions - conventions that manipulate the final model once it is created - to handle advanced scenarios.</span></span> <span data-ttu-id="ce404-115">如需詳細資訊，請參閱以 [模型為基礎的慣例](xref:ef6/modeling/code-first/conventions/model)。</span><span class="sxs-lookup"><span data-stu-id="ce404-115">For more information, see [Model-Based Conventions](xref:ef6/modeling/code-first/conventions/model).</span></span>

 

## <a name="our-model"></a><span data-ttu-id="ce404-116">我們的模型</span><span class="sxs-lookup"><span data-stu-id="ce404-116">Our Model</span></span>

<span data-ttu-id="ce404-117">讓我們從定義可搭配慣例使用的簡單模型開始。</span><span class="sxs-lookup"><span data-stu-id="ce404-117">Let's start by defining a simple model that we can use with our conventions.</span></span> <span data-ttu-id="ce404-118">將下列類別新增至您的專案。</span><span class="sxs-lookup"><span data-stu-id="ce404-118">Add the following classes to your project.</span></span>

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

 

## <a name="introducing-custom-conventions"></a><span data-ttu-id="ce404-119">自訂慣例簡介</span><span class="sxs-lookup"><span data-stu-id="ce404-119">Introducing Custom Conventions</span></span>

<span data-ttu-id="ce404-120">讓我們撰寫一項慣例，將名為 Key 的任何屬性設定為其實體類型的主鍵。</span><span class="sxs-lookup"><span data-stu-id="ce404-120">Let’s write a convention that configures any property named Key to be the primary key for its entity type.</span></span>

<span data-ttu-id="ce404-121">模型產生器上已啟用慣例，可透過覆寫內容中的 OnModelCreating 來存取。</span><span class="sxs-lookup"><span data-stu-id="ce404-121">Conventions are enabled on the model builder, which can be accessed by overriding OnModelCreating in the context.</span></span> <span data-ttu-id="ce404-122">更新 ProductCoNtext 類別，如下所示：</span><span class="sxs-lookup"><span data-stu-id="ce404-122">Update the ProductContext class as follows:</span></span>

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

<span data-ttu-id="ce404-123">現在，模型中名為 Key 的任何屬性，將會設定為其所屬的任何實體的主鍵。</span><span class="sxs-lookup"><span data-stu-id="ce404-123">Now, any property in our model named Key will be configured as the primary key of whatever entity its part of.</span></span>

<span data-ttu-id="ce404-124">我們也可以篩選要設定的屬性類型，讓我們的慣例更明確：</span><span class="sxs-lookup"><span data-stu-id="ce404-124">We could also make our conventions more specific by filtering on the type of property that we are going to configure:</span></span>

``` csharp
    modelBuilder.Properties<int>()
                .Where(p => p.Name == "Key")
                .Configure(p => p.IsKey());
```

<span data-ttu-id="ce404-125">這會將名為 Key 的所有屬性設定為其實體的主鍵，但只有在它們是整數時。</span><span class="sxs-lookup"><span data-stu-id="ce404-125">This will configure all properties called Key to be the primary key of their entity, but only if they are an integer.</span></span>

<span data-ttu-id="ce404-126">IsKey 方法有一個有趣的功能，就是它是加法。</span><span class="sxs-lookup"><span data-stu-id="ce404-126">An interesting feature of the IsKey method is that it is additive.</span></span> <span data-ttu-id="ce404-127">這表示，如果您在多個屬性上呼叫 IsKey，它們全都會成為複合索引鍵的一部分。</span><span class="sxs-lookup"><span data-stu-id="ce404-127">Which means that if you call IsKey on multiple properties and they will all become part of a composite key.</span></span> <span data-ttu-id="ce404-128">有一點要注意的是，當您為索引鍵指定多個屬性時，您也必須指定這些屬性的順序。</span><span class="sxs-lookup"><span data-stu-id="ce404-128">The one caveat for this is that when you specify multiple properties for a key you must also specify an order for those properties.</span></span> <span data-ttu-id="ce404-129">若要這麼做，您可以呼叫 HasColumnOrder 方法，如下所示：</span><span class="sxs-lookup"><span data-stu-id="ce404-129">You can do this by calling the HasColumnOrder method like below:</span></span>

``` csharp
    modelBuilder.Properties<int>()
                .Where(x => x.Name == "Key")
                .Configure(x => x.IsKey().HasColumnOrder(1));

    modelBuilder.Properties()
                .Where(x => x.Name == "Name")
                .Configure(x => x.IsKey().HasColumnOrder(2));
```

<span data-ttu-id="ce404-130">這段程式碼會將模型中的型別設定為具有由 int 索引鍵資料行和字串名稱資料行所組成的複合索引鍵。</span><span class="sxs-lookup"><span data-stu-id="ce404-130">This code will configure the types in our model to have a composite key consisting of the int Key column and the string Name column.</span></span> <span data-ttu-id="ce404-131">如果我們在設計工具中看到模型，它看起來會像這樣：</span><span class="sxs-lookup"><span data-stu-id="ce404-131">If we view the model in the designer it would look like this:</span></span>

![複合索引鍵](~/ef6/media/compositekey.png)

<span data-ttu-id="ce404-133">屬性慣例的另一個範例，是在模型中設定所有 DateTime 屬性，以對應至 SQL Server 中的 datetime2 型別，而非 DateTime。</span><span class="sxs-lookup"><span data-stu-id="ce404-133">Another example of property conventions is to configure all DateTime properties in my model to map to the datetime2 type in SQL Server instead of datetime.</span></span> <span data-ttu-id="ce404-134">您可以使用下列內容達成此目的：</span><span class="sxs-lookup"><span data-stu-id="ce404-134">You can achieve this with the following:</span></span>

``` csharp
    modelBuilder.Properties<DateTime>()
                .Configure(c => c.HasColumnType("datetime2"));
```

 

## <a name="convention-classes"></a><span data-ttu-id="ce404-135">慣例類別</span><span class="sxs-lookup"><span data-stu-id="ce404-135">Convention Classes</span></span>

<span data-ttu-id="ce404-136">定義慣例的另一種方式是使用慣例類別來封裝您的慣例。</span><span class="sxs-lookup"><span data-stu-id="ce404-136">Another way of defining conventions is to use a Convention Class to encapsulate your convention.</span></span> <span data-ttu-id="ce404-137">使用慣例類別時，您會建立一個繼承自 ModelConfiguration 命名空間中慣例類別的型別。</span><span class="sxs-lookup"><span data-stu-id="ce404-137">When using a Convention Class then you create a type that inherits from the Convention class in the System.Data.Entity.ModelConfiguration.Conventions namespace.</span></span>

<span data-ttu-id="ce404-138">我們可以藉由執行下列步驟，以稍早所示的 datetime2 慣例來建立慣例類別：</span><span class="sxs-lookup"><span data-stu-id="ce404-138">We can create a Convention Class with the datetime2 convention that we showed earlier by doing the following:</span></span>

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

<span data-ttu-id="ce404-139">若要告訴 EF 使用此慣例，請將它新增至 OnModelCreating 的慣例集合，如果您已遵循本逐步解說的說明，將會如下所示：</span><span class="sxs-lookup"><span data-stu-id="ce404-139">To tell EF to use this convention you add it to the Conventions collection in OnModelCreating, which if you’ve been following along with the walkthrough will look like this:</span></span>

``` csharp
    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Properties<int>()
                    .Where(p => p.Name.EndsWith("Key"))
                    .Configure(p => p.IsKey());

        modelBuilder.Conventions.Add(new DateTime2Convention());
    }
```

<span data-ttu-id="ce404-140">如您所見，我們會將慣例的實例新增至慣例集合。</span><span class="sxs-lookup"><span data-stu-id="ce404-140">As you can see we add an instance of our convention to the conventions collection.</span></span> <span data-ttu-id="ce404-141">繼承自慣例提供在小組或專案之間分組和共用慣例的便利方式。</span><span class="sxs-lookup"><span data-stu-id="ce404-141">Inheriting from Convention provides a convenient way of grouping and sharing conventions across teams or projects.</span></span> <span data-ttu-id="ce404-142">例如，您可以有一個類別庫，其中包含您所有組織專案都使用的一組通用慣例。</span><span class="sxs-lookup"><span data-stu-id="ce404-142">You could, for example, have a class library with a common set of conventions that all of your organizations projects use.</span></span>

 

## <a name="custom-attributes"></a><span data-ttu-id="ce404-143">自訂屬性</span><span class="sxs-lookup"><span data-stu-id="ce404-143">Custom Attributes</span></span>

<span data-ttu-id="ce404-144">慣例的另一個絕佳用途，是要在設定模型時啟用新的屬性。</span><span class="sxs-lookup"><span data-stu-id="ce404-144">Another great use of conventions is to enable new attributes to be used when configuring a model.</span></span> <span data-ttu-id="ce404-145">為了說明這一點，讓我們建立一個屬性，讓我們可以用來將字串屬性標示為非 Unicode。</span><span class="sxs-lookup"><span data-stu-id="ce404-145">To illustrate this, let’s create an attribute that we can use to mark String properties as non-Unicode.</span></span>

``` csharp
    [AttributeUsage(AttributeTargets.Property, AllowMultiple = false)]
    public class NonUnicode : Attribute
    {
    }
```

<span data-ttu-id="ce404-146">現在，讓我們建立將此屬性套用至模型的慣例：</span><span class="sxs-lookup"><span data-stu-id="ce404-146">Now, let’s create a convention to apply this attribute to our model:</span></span>

``` csharp
    modelBuilder.Properties()
                .Where(x => x.GetCustomAttributes(false).OfType<NonUnicode>().Any())
                .Configure(c => c.IsUnicode(false));
```

<span data-ttu-id="ce404-147">使用這個慣例，我們可以將 NonUnicode 屬性加入至任何字串屬性，這表示資料庫中的資料行會儲存為 Varchar 而非 Nvarchar。</span><span class="sxs-lookup"><span data-stu-id="ce404-147">With this convention we can add the NonUnicode attribute to any of our string properties, which means the column in the database will be stored as varchar instead of nvarchar.</span></span>

<span data-ttu-id="ce404-148">有一點要注意的是，如果您將 NonUnicode 屬性放在字串屬性以外的任何字元，則會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="ce404-148">One thing to note about this convention is that if you put the NonUnicode attribute on anything other than a string property then it will throw an exception.</span></span> <span data-ttu-id="ce404-149">這樣做的原因是因為您無法在字串以外的任何類型上設定 IsUnicode。</span><span class="sxs-lookup"><span data-stu-id="ce404-149">It does this because you cannot configure IsUnicode on any type other than a string.</span></span> <span data-ttu-id="ce404-150">如果發生這種情況，您可以讓您的慣例更明確，讓它篩選掉不是字串的任何事物。</span><span class="sxs-lookup"><span data-stu-id="ce404-150">If this happens, then you can make your convention more specific, so that it filters out anything that isn’t a string.</span></span>

<span data-ttu-id="ce404-151">雖然上述慣例適用于定義自訂屬性，但還有另一個 API 可能更容易使用，特別是當您想要使用屬性類別的屬性時。</span><span class="sxs-lookup"><span data-stu-id="ce404-151">While the above convention works for defining custom attributes there is another API that can be much easier to use, especially when you want to use properties from the attribute class.</span></span>

<span data-ttu-id="ce404-152">在此範例中，我們要更新屬性，並將其變更為 IsUnicode 屬性，使其看起來像這樣：</span><span class="sxs-lookup"><span data-stu-id="ce404-152">For this example we are going to update our attribute and change it to an IsUnicode attribute, so it looks like this:</span></span>

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

<span data-ttu-id="ce404-153">一旦有了這種情況之後，我們就可以在屬性上設定 bool，以告知慣例屬性是否應該是 Unicode。</span><span class="sxs-lookup"><span data-stu-id="ce404-153">Once we have this, we can set a bool on our attribute to tell the convention whether or not a property should be Unicode.</span></span> <span data-ttu-id="ce404-154">我們可以藉由存取設定類別的 ClrProperty 來達成此目的，如下所示：</span><span class="sxs-lookup"><span data-stu-id="ce404-154">We could do this in the convention we have already by accessing the ClrProperty of the configuration class like this:</span></span>

``` csharp
    modelBuilder.Properties()
                .Where(x => x.GetCustomAttributes(false).OfType<IsUnicode>().Any())
                .Configure(c => c.IsUnicode(c.ClrPropertyInfo.GetCustomAttribute<IsUnicode>().Unicode));
```

<span data-ttu-id="ce404-155">這很簡單，但使用「慣例 API」的 Having 方法可以更簡潔地達成此目的。</span><span class="sxs-lookup"><span data-stu-id="ce404-155">This is easy enough, but there is a more succinct way of achieving this by using the Having method of the conventions API.</span></span> <span data-ttu-id="ce404-156">Having 方法具有 Func PropertyInfo 型別的參數 &lt; ， &gt; 它接受的 PropertyInfo 與 Where 方法相同，但預期會傳回物件。</span><span class="sxs-lookup"><span data-stu-id="ce404-156">The Having method has a parameter of type Func&lt;PropertyInfo, T&gt; which accepts the PropertyInfo the same as the Where method, but is expected to return an object.</span></span> <span data-ttu-id="ce404-157">如果傳回的物件為 null，則不會設定此屬性，這表示您可以像在 Where 一樣地篩選出屬性，但它也不同，因為它也會捕捉傳回的物件，並將它傳遞至設定方法。</span><span class="sxs-lookup"><span data-stu-id="ce404-157">If the returned object is null then the property will not be configured, which means you can filter out properties with it just like Where, but it is different in that it will also capture the returned object and pass it to the Configure method.</span></span> <span data-ttu-id="ce404-158">其運作方式如下所示：</span><span class="sxs-lookup"><span data-stu-id="ce404-158">This works like the following:</span></span>

``` csharp
    modelBuilder.Properties()
                .Having(x =>x.GetCustomAttributes(false).OfType<IsUnicode>().FirstOrDefault())
                .Configure((config, att) => config.IsUnicode(att.Unicode));
```

<span data-ttu-id="ce404-159">自訂屬性不是使用 Having 方法的唯一原因，它很適合您在設定類型或屬性時所要篩選的某個內容的任何位置。</span><span class="sxs-lookup"><span data-stu-id="ce404-159">Custom attributes are not the only reason to use the Having method, it is useful anywhere that you need to reason about something that you are filtering on when configuring your types or properties.</span></span>

 

## <a name="configuring-types"></a><span data-ttu-id="ce404-160">設定類型</span><span class="sxs-lookup"><span data-stu-id="ce404-160">Configuring Types</span></span>

<span data-ttu-id="ce404-161">到目前為止，我們所有的慣例都是針對屬性，但是慣例 API 還有另一個區域，可在您的模型中設定類型。</span><span class="sxs-lookup"><span data-stu-id="ce404-161">So far all of our conventions have been for properties, but there is another area of the conventions API for configuring the types in your model.</span></span> <span data-ttu-id="ce404-162">此體驗類似于目前為止所見的慣例，但 [設定] 內的選項會在實體上，而不是屬性層級。</span><span class="sxs-lookup"><span data-stu-id="ce404-162">The experience is similar to the conventions we have seen so far, but the options inside configure will be at the entity instead of property level.</span></span>

<span data-ttu-id="ce404-163">類型層級慣例的其中一件事，就是變更資料表命名慣例，以對應至與 EF 預設值不同的現有架構，或使用不同的命名慣例來建立新的資料庫。</span><span class="sxs-lookup"><span data-stu-id="ce404-163">One of the things that Type level conventions can be really useful for is changing the table naming convention, either to map to an existing schema that differs from the EF default or to create a new database with a different naming convention.</span></span> <span data-ttu-id="ce404-164">若要這樣做，我們必須先有方法可以接受模型中類型的 TypeInfo，並傳回該型別的資料表名稱應該是什麼：</span><span class="sxs-lookup"><span data-stu-id="ce404-164">To do this we first need a method that can accept the TypeInfo for a type in our model and return what the table name for that type should be:</span></span>

``` csharp
    private string GetTableName(Type type)
    {
        var result = Regex.Replace(type.Name, ".[A-Z]", m => m.Value[0] + "_" + m.Value[1]);

        return result.ToLower();
    }
```

<span data-ttu-id="ce404-165">這個方法會採用類型，並傳回使用小寫和底線而非 CamelCase 的字串。</span><span class="sxs-lookup"><span data-stu-id="ce404-165">This method takes a type and returns a string that uses lower case with underscores instead of CamelCase.</span></span> <span data-ttu-id="ce404-166">在我們的模型中，這表示 ProductCategory 類別會對應到稱為 product category 的資料表， \_ 而不是 ProductCategories。</span><span class="sxs-lookup"><span data-stu-id="ce404-166">In our model this means that the ProductCategory class will be mapped to a table called product\_category instead of ProductCategories.</span></span>

<span data-ttu-id="ce404-167">有了這個方法之後，就可以用像這樣的慣例來呼叫它：</span><span class="sxs-lookup"><span data-stu-id="ce404-167">Once we have that method we can call it in a convention like this:</span></span>

``` csharp
    modelBuilder.Types()
                .Configure(c => c.ToTable(GetTableName(c.ClrType)));
```

<span data-ttu-id="ce404-168">此慣例會將模型中的每個型別設定為對應至 GetTableName 方法所傳回的資料表名稱。</span><span class="sxs-lookup"><span data-stu-id="ce404-168">This convention configures every type in our model to map to the table name that is returned from our GetTableName method.</span></span> <span data-ttu-id="ce404-169">這個慣例相當於使用流暢的 API 針對模型中的每個實體呼叫 ToTable 方法。</span><span class="sxs-lookup"><span data-stu-id="ce404-169">This convention is the equivalent to calling the ToTable method for each entity in the model using the Fluent API.</span></span>

<span data-ttu-id="ce404-170">有一點要注意的是，當您呼叫 ToTable 時，EF 會將您提供的字串做為精確的資料表名稱，而不是在判斷資料表名稱時，通常會執行的任何複數表示。</span><span class="sxs-lookup"><span data-stu-id="ce404-170">One thing to note about this is that when you call ToTable EF will take the string that you provide as the exact table name, without any of the pluralization that it would normally do when determining table names.</span></span> <span data-ttu-id="ce404-171">這就是為什麼我們慣例中的資料表名稱是產品類別目錄， \_ 而不是產品類別目錄的原因 \_ 。</span><span class="sxs-lookup"><span data-stu-id="ce404-171">This is why the table name from our convention is product\_category instead of product\_categories.</span></span> <span data-ttu-id="ce404-172">我們可以藉由對複數表示服務進行呼叫，在我們的慣例中解決此問題。</span><span class="sxs-lookup"><span data-stu-id="ce404-172">We can resolve that in our convention by making a call to the pluralization service ourselves.</span></span>

<span data-ttu-id="ce404-173">在下列程式碼中，我們將使用 EF6 中加入的相依性 [解析](xref:ef6/fundamentals/configuring/dependency-resolution) 功能，來抓取 EF 已使用的複數表示服務，並複數化我們的資料表名稱。</span><span class="sxs-lookup"><span data-stu-id="ce404-173">In the following code we will use the [Dependency Resolution](xref:ef6/fundamentals/configuring/dependency-resolution) feature added in EF6 to retrieve the pluralization service that EF would have used and pluralize our table name.</span></span>

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
> <span data-ttu-id="ce404-174">GetService 的泛型版本是 DependencyResolution 命名空間中的擴充方法，您必須將 using 語句加入至您的內容，才能使用它。</span><span class="sxs-lookup"><span data-stu-id="ce404-174">The generic version of GetService is an extension method in the System.Data.Entity.Infrastructure.DependencyResolution namespace, you will need to add a using statement to your context in order to use it.</span></span>

### <a name="totable-and-inheritance"></a><span data-ttu-id="ce404-175">ToTable 和繼承</span><span class="sxs-lookup"><span data-stu-id="ce404-175">ToTable and Inheritance</span></span>

<span data-ttu-id="ce404-176">ToTable 的另一個重要層面是，如果您明確地將型別對應到指定的資料表，則可以改變 EF 將使用的對應策略。</span><span class="sxs-lookup"><span data-stu-id="ce404-176">Another important aspect of ToTable is that if you explicitly map a type to a given table, then you can alter the mapping strategy that EF will use.</span></span> <span data-ttu-id="ce404-177">如果您針對繼承階層架構中的每個型別呼叫 ToTable，然後將型別名稱當作資料表的名稱來傳遞，那麼您就可以將每個階層的預設資料表 (TPH) 對應策略變更為每個類型的資料表 (TPT) 。</span><span class="sxs-lookup"><span data-stu-id="ce404-177">If you call ToTable for every type in an inheritance hierarchy, passing the type name as the name of the table like we did above, then you will change the default Table-Per-Hierarchy (TPH) mapping strategy to Table-Per-Type (TPT).</span></span> <span data-ttu-id="ce404-178">描述這項工作的最佳方式是 whith 具體的範例：</span><span class="sxs-lookup"><span data-stu-id="ce404-178">The best way to describe this is whith a concrete example:</span></span>

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

<span data-ttu-id="ce404-179">根據預設，employee 和 manager 都會對應到資料庫中 (員工) 的相同資料表。</span><span class="sxs-lookup"><span data-stu-id="ce404-179">By default both employee and manager are mapped to the same table (Employees) in the database.</span></span> <span data-ttu-id="ce404-180">資料表將會包含具有鑒別子資料行的員工和經理，以告訴您每個資料列中儲存的實例類型。</span><span class="sxs-lookup"><span data-stu-id="ce404-180">The table will contain both employees and managers with a discriminator column that will tell you what type of instance is stored in each row.</span></span> <span data-ttu-id="ce404-181">這是 TPH 對應，因為階層有單一資料表。</span><span class="sxs-lookup"><span data-stu-id="ce404-181">This is TPH mapping as there is a single table for the hierarchy.</span></span> <span data-ttu-id="ce404-182">但是，如果您在這兩個 classe 上呼叫 ToTable，則每個類型都會改為對應至它自己的資料表（也稱為 TPT），因為每個類型都有自己的資料表。</span><span class="sxs-lookup"><span data-stu-id="ce404-182">However, if you call ToTable on both classe then each type will instead be mapped to its own table, also known as TPT since each type has its own table.</span></span>

``` csharp
    modelBuilder.Types()
                .Configure(c=>c.ToTable(c.ClrType.Name));
```

<span data-ttu-id="ce404-183">上述程式碼會對應到如下所示的資料表結構：</span><span class="sxs-lookup"><span data-stu-id="ce404-183">The code above will map to a table structure that looks like the following:</span></span>

![tpt 範例](~/ef6/media/tptexample.jpg)

<span data-ttu-id="ce404-185">您可以透過幾種方式來避免這種情況，並維持預設的 TPH 對應：</span><span class="sxs-lookup"><span data-stu-id="ce404-185">You can avoid this, and maintain the default TPH mapping, in a couple ways:</span></span>

1.  <span data-ttu-id="ce404-186">針對階層中的每個類型，使用相同的資料表名稱來呼叫 ToTable。</span><span class="sxs-lookup"><span data-stu-id="ce404-186">Call ToTable with the same table name for each type in the hierarchy.</span></span>
2.  <span data-ttu-id="ce404-187">請只針對階層的基類（在我們的範例中為 employee）呼叫 ToTable。</span><span class="sxs-lookup"><span data-stu-id="ce404-187">Call ToTable only on the base class of the hierarchy, in our example that would be employee.</span></span>

 

## <a name="execution-order"></a><span data-ttu-id="ce404-188">執行順序</span><span class="sxs-lookup"><span data-stu-id="ce404-188">Execution Order</span></span>

<span data-ttu-id="ce404-189">慣例的運作方式與流暢的 API 相同。</span><span class="sxs-lookup"><span data-stu-id="ce404-189">Conventions operate in a last wins manner, the same as the Fluent API.</span></span> <span data-ttu-id="ce404-190">這表示，如果您撰寫兩個慣例來設定相同屬性的相同選項，則最後一個會執行 wins。</span><span class="sxs-lookup"><span data-stu-id="ce404-190">What this means is that if you write two conventions that configure the same option of the same property, then the last one to execute wins.</span></span> <span data-ttu-id="ce404-191">例如，在下列程式碼中，所有字串的最大長度都會設定為500，但接著會在模型中設定名為 Name 的所有屬性，使其長度上限為250。</span><span class="sxs-lookup"><span data-stu-id="ce404-191">As an example, in the code below the max length of all strings is set to 500 but we then configure all properties called Name in the model to have a max length of 250.</span></span>

``` csharp
    modelBuilder.Properties<string>()
                .Configure(c => c.HasMaxLength(500));

    modelBuilder.Properties<string>()
                .Where(x => x.Name == "Name")
                .Configure(c => c.HasMaxLength(250));
```

<span data-ttu-id="ce404-192">由於將 [最大長度] 設定為250的慣例是在將所有字串設為500的之後，因此模型中名為 Name 的所有屬性都會有 MaxLength 250，而任何其他字串（例如描述）會是500。</span><span class="sxs-lookup"><span data-stu-id="ce404-192">Because the convention to set max length to 250 is after the one that sets all strings to 500, all the properties called Name in our model will have a MaxLength of 250 while any other strings, such as descriptions, would be 500.</span></span> <span data-ttu-id="ce404-193">以這種方式使用慣例，表示您可以在模型中提供類型或屬性的一般慣例，然後針對不同的子集覆寫它們。</span><span class="sxs-lookup"><span data-stu-id="ce404-193">Using conventions in this way means that you can provide a general convention for types or properties in your model and then overide them for subsets that are different.</span></span>

<span data-ttu-id="ce404-194">流暢的 API 和資料批註也可以用來覆寫特定情況下的慣例。</span><span class="sxs-lookup"><span data-stu-id="ce404-194">The Fluent API and Data Annotations can also be used to override a convention in specific cases.</span></span> <span data-ttu-id="ce404-195">在上述範例中，如果我們使用了流暢的 API 來設定屬性的最大長度，則在慣例之前或之後，我們可以將它放在慣例之前或之後，因為更明確的流暢 API 將會贏得更一般的設定慣例。</span><span class="sxs-lookup"><span data-stu-id="ce404-195">In our example above if we had used the Fluent API to set the max length of a property then we could have put it before or after the convention, because the more specific Fluent API will win over the more general Configuration Convention.</span></span>

 

## <a name="built-in-conventions"></a><span data-ttu-id="ce404-196">內建慣例</span><span class="sxs-lookup"><span data-stu-id="ce404-196">Built-in Conventions</span></span>

<span data-ttu-id="ce404-197">因為自訂慣例可能會受到預設 Code First 慣例的影響，所以將慣例新增為在另一個慣例之前或之後執行，可能會很有用。</span><span class="sxs-lookup"><span data-stu-id="ce404-197">Because custom conventions could be affected by the default Code First conventions, it can be useful to add conventions to run before or after another convention.</span></span> <span data-ttu-id="ce404-198">若要這樣做，您可以在衍生的 DbCoNtext 上使用慣例集合的 AddBefore 和 AddAfter 方法。</span><span class="sxs-lookup"><span data-stu-id="ce404-198">To do this you can use the AddBefore and AddAfter methods of the Conventions collection on your derived DbContext.</span></span> <span data-ttu-id="ce404-199">下列程式碼會新增我們稍早建立的慣例類別，使其可在內建的金鑰探索慣例之前執行。</span><span class="sxs-lookup"><span data-stu-id="ce404-199">The following code would add the convention class we created earlier so that it will run before the built in key discovery convention.</span></span>

``` csharp
    modelBuilder.Conventions.AddBefore<IdKeyDiscoveryConvention>(new DateTime2Convention());
```

<span data-ttu-id="ce404-200">當您新增需要在內建慣例之前或之後執行的慣例時，這會是最常用的功能，您可以在這裡找到內建的慣例清單： [ModelConfiguration 命名空間](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx)。</span><span class="sxs-lookup"><span data-stu-id="ce404-200">This is going to be of the most use when adding conventions that need to run before or after the built in conventions, a list of the built in conventions can be found here: [System.Data.Entity.ModelConfiguration.Conventions Namespace](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx).</span></span>

<span data-ttu-id="ce404-201">您也可以移除您不想套用至模型的慣例。</span><span class="sxs-lookup"><span data-stu-id="ce404-201">You can also remove conventions that you do not want applied to your model.</span></span> <span data-ttu-id="ce404-202">若要移除慣例，請使用 Remove 方法。</span><span class="sxs-lookup"><span data-stu-id="ce404-202">To remove a convention, use the Remove method.</span></span> <span data-ttu-id="ce404-203">以下是移除 PluralizingTableNameConvention 的範例。</span><span class="sxs-lookup"><span data-stu-id="ce404-203">Here is an example of removing the PluralizingTableNameConvention.</span></span>

``` csharp
    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Conventions.Remove<PluralizingTableNameConvention>();
    }
```
