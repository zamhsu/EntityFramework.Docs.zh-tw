---
title: Code First 資料批註-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 80abefbd-23c9-4fce-9cd3-520e5df9856e
ms.openlocfilehash: 9fac2a90c46d78ff5fd632800cc0050276467773
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78419182"
---
# <a name="code-first-data-annotations"></a><span data-ttu-id="5ea98-102">Code First 資料批註</span><span class="sxs-lookup"><span data-stu-id="5ea98-102">Code First Data Annotations</span></span>
> [!NOTE]
> <span data-ttu-id="5ea98-103">**僅限 ef 4.1 [僅限**]-此頁面中所討論的功能、api 等已在 Entity Framework 4.1 中引進。</span><span class="sxs-lookup"><span data-stu-id="5ea98-103">**EF4.1 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 4.1.</span></span> <span data-ttu-id="5ea98-104">如果您使用的是較舊的版本，則不會套用本資訊的部分或全部。</span><span class="sxs-lookup"><span data-stu-id="5ea98-104">If you are using an earlier version, some or all of this information does not apply.</span></span>

<span data-ttu-id="5ea98-105">此頁面上的內容可從原本由 Julie Lerman （\<http://thedatafarm.com>)所撰寫的文章進行調整。</span><span class="sxs-lookup"><span data-stu-id="5ea98-105">The content on this page is adapted from an article originally written by Julie Lerman (\<http://thedatafarm.com>).</span></span>

<span data-ttu-id="5ea98-106">Entity Framework Code First 可讓您使用自己的網域類別來代表 EF 依賴來執行查詢、變更追蹤和更新函數的模型。</span><span class="sxs-lookup"><span data-stu-id="5ea98-106">Entity Framework Code First allows you to use your own domain classes to represent the model that EF relies on to perform querying, change tracking, and updating functions.</span></span> <span data-ttu-id="5ea98-107">Code First 利用稱為「設定的慣例」的程式設計模式。</span><span class="sxs-lookup"><span data-stu-id="5ea98-107">Code First leverages a programming pattern referred to as 'convention over configuration.'</span></span> <span data-ttu-id="5ea98-108">Code First 會假設您的類別遵循 Entity Framework 的慣例，而在此情況下，將會自動處理如何執行其作業。</span><span class="sxs-lookup"><span data-stu-id="5ea98-108">Code First will assume that your classes follow the conventions of Entity Framework, and in that case, will automatically work out how to perform its job.</span></span> <span data-ttu-id="5ea98-109">不過，如果您的類別未遵循這些慣例，您就能夠將設定新增至您的類別，以提供 EF 必要的資訊。</span><span class="sxs-lookup"><span data-stu-id="5ea98-109">However, if your classes do not follow those conventions, you have the ability to add configurations to your classes to provide EF with the requisite information.</span></span>

<span data-ttu-id="5ea98-110">Code First 提供您兩種方式，將這些設定新增至您的類別。</span><span class="sxs-lookup"><span data-stu-id="5ea98-110">Code First gives you two ways to add these configurations to your classes.</span></span> <span data-ttu-id="5ea98-111">其中一個是使用稱為 DataAnnotations 的簡單屬性，第二個則是使用 Code First 的流暢 API，這可讓您在程式碼中以命令方式描述設定。</span><span class="sxs-lookup"><span data-stu-id="5ea98-111">One is using simple attributes called DataAnnotations, and the second is using Code First’s Fluent API, which provides you with a way to describe configurations imperatively, in code.</span></span>

<span data-ttu-id="5ea98-112">本文將著重于使用 DataAnnotations （在 System.workflow.componentmodel.activity. DataAnnotations 命名空間中）來設定您的類別，並反白顯示最常需要的設定。</span><span class="sxs-lookup"><span data-stu-id="5ea98-112">This article will focus on using DataAnnotations (in the System.ComponentModel.DataAnnotations namespace) to configure your classes – highlighting the most commonly needed configurations.</span></span> <span data-ttu-id="5ea98-113">許多 .NET 應用程式（例如 ASP.NET MVC）也都能理解 DataAnnotations，這可讓這些應用程式利用相同的注釋來進行用戶端驗證。</span><span class="sxs-lookup"><span data-stu-id="5ea98-113">DataAnnotations are also understood by a number of .NET applications, such as ASP.NET MVC which allows these applications to leverage the same annotations for client-side validations.</span></span>


## <a name="the-model"></a><span data-ttu-id="5ea98-114">模型</span><span class="sxs-lookup"><span data-stu-id="5ea98-114">The model</span></span>

<span data-ttu-id="5ea98-115">我將使用一組簡單的類別來示範 Code First DataAnnotations： Blog 和 Post。</span><span class="sxs-lookup"><span data-stu-id="5ea98-115">I’ll demonstrate Code First DataAnnotations with a simple pair of classes: Blog and Post.</span></span>

``` csharp
    public class Blog
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string BloggerName { get; set;}
        public virtual ICollection<Post> Posts { get; set; }
    }

    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public DateTime DateCreated { get; set; }
        public string Content { get; set; }
        public int BlogId { get; set; }
        public ICollection<Comment> Comments { get; set; }
    }
```

<span data-ttu-id="5ea98-116">就像是一樣，Blog 和 Post 類別可以方便遵循 code first 慣例，而且不需要調整就能啟用 EF 相容性。</span><span class="sxs-lookup"><span data-stu-id="5ea98-116">As they are, the Blog and Post classes conveniently follow code first convention and require no tweaks to enable EF compatability.</span></span> <span data-ttu-id="5ea98-117">不過，您也可以使用批註，將有關類別及其對應之資料庫的詳細資訊提供給 EF。</span><span class="sxs-lookup"><span data-stu-id="5ea98-117">However, you can also use the annotations to provide more information to EF about the classes and the database to which they map.</span></span>

 

## <a name="key"></a><span data-ttu-id="5ea98-118">Key</span><span class="sxs-lookup"><span data-stu-id="5ea98-118">Key</span></span>

<span data-ttu-id="5ea98-119">Entity Framework 依賴每個實體具有用於實體追蹤的索引鍵值。</span><span class="sxs-lookup"><span data-stu-id="5ea98-119">Entity Framework relies on every entity having a key value that is used for entity tracking.</span></span> <span data-ttu-id="5ea98-120">Code First 的其中一個慣例是隱含的索引鍵屬性;Code First 會尋找名為 "Id" 的屬性，或類別名稱和 "Id" 的組合，例如 "BlogId"。</span><span class="sxs-lookup"><span data-stu-id="5ea98-120">One convention of Code First is implicit key properties; Code First will look for a property named “Id”, or a combination of class name and “Id”, such as “BlogId”.</span></span> <span data-ttu-id="5ea98-121">這個屬性會對應至資料庫中的主鍵資料行。</span><span class="sxs-lookup"><span data-stu-id="5ea98-121">This property will map to a primary key column in the database.</span></span>

<span data-ttu-id="5ea98-122">[Blog] 和 [Post] 類別都遵循此慣例。</span><span class="sxs-lookup"><span data-stu-id="5ea98-122">The Blog and Post classes both follow this convention.</span></span> <span data-ttu-id="5ea98-123">如果沒有的話，該怎麼辦？</span><span class="sxs-lookup"><span data-stu-id="5ea98-123">What if they didn’t?</span></span> <span data-ttu-id="5ea98-124">如果 Blog 使用名稱*PrimaryTrackingKey* ，或甚至是*foo*，該怎麼辦？</span><span class="sxs-lookup"><span data-stu-id="5ea98-124">What if Blog used the name *PrimaryTrackingKey* instead, or even *foo*?</span></span> <span data-ttu-id="5ea98-125">如果 code first 找不到符合此慣例的屬性，就會擲回例外狀況，因為 Entity Framework 的需求必須要有索引鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="5ea98-125">If code first does not find a property that matches this convention it will throw an exception because of Entity Framework’s requirement that you must have a key property.</span></span> <span data-ttu-id="5ea98-126">您可以使用索引鍵注釋來指定要使用哪一個屬性做為 EntityKey。</span><span class="sxs-lookup"><span data-stu-id="5ea98-126">You can use the key annotation to specify which property is to be used as the EntityKey.</span></span>

``` csharp
    public class Blog
    {
        [Key]
        public int PrimaryTrackingKey { get; set; }
        public string Title { get; set; }
        public string BloggerName { get; set;}
        public virtual ICollection<Post> Posts { get; set; }
    }
```

<span data-ttu-id="5ea98-127">如果您使用 code first 的資料庫產生功能，則 Blog 資料表會有一個名為 PrimaryTrackingKey 的主鍵資料行，其預設也會定義為身分識別。</span><span class="sxs-lookup"><span data-stu-id="5ea98-127">If you are using code first’s database generation feature, the Blog table will have a primary key column named PrimaryTrackingKey, which is also defined as Identity by default.</span></span>

![具有主要金鑰的 Blog 資料表](~/ef6/media/jj591583-figure01.png)

### <a name="composite-keys"></a><span data-ttu-id="5ea98-129">複合索引鍵</span><span class="sxs-lookup"><span data-stu-id="5ea98-129">Composite keys</span></span>

<span data-ttu-id="5ea98-130">Entity Framework 支援複合索引鍵-由一個以上的屬性所組成的主要索引鍵。</span><span class="sxs-lookup"><span data-stu-id="5ea98-130">Entity Framework supports composite keys - primary keys that consist of more than one property.</span></span> <span data-ttu-id="5ea98-131">例如，您可以擁有一個 Passport 類別，其主要金鑰是 PassportNumber 和 IssuingCountry 的組合。</span><span class="sxs-lookup"><span data-stu-id="5ea98-131">For example, you could have a Passport class whose primary key is a combination of PassportNumber and IssuingCountry.</span></span>

``` csharp
    public class Passport
    {
        [Key]
        public int PassportNumber { get; set; }
        [Key]
        public string IssuingCountry { get; set; }
        public DateTime Issued { get; set; }
        public DateTime Expires { get; set; }
    }
```

<span data-ttu-id="5ea98-132">嘗試在 EF 模型中使用上述類別會產生 `InvalidOperationException`：</span><span class="sxs-lookup"><span data-stu-id="5ea98-132">Attempting to use the above class in your EF model would result in an `InvalidOperationException`:</span></span>

<span data-ttu-id="5ea98-133">*無法判斷類型 ' Passport ' 的複合主要索引鍵排序。使用 ColumnAttribute 或 HasKey 方法來指定複合主鍵的順序。*</span><span class="sxs-lookup"><span data-stu-id="5ea98-133">*Unable to determine composite primary key ordering for type 'Passport'. Use the ColumnAttribute or the HasKey method to specify an order for composite primary keys.*</span></span>

<span data-ttu-id="5ea98-134">為了使用複合索引鍵，Entity Framework 需要您定義索引鍵屬性的順序。</span><span class="sxs-lookup"><span data-stu-id="5ea98-134">In order to use composite keys, Entity Framework requires you to define an order for the key properties.</span></span> <span data-ttu-id="5ea98-135">若要這麼做，您可以使用資料行批註來指定訂單。</span><span class="sxs-lookup"><span data-stu-id="5ea98-135">You can do this by using the Column annotation to specify an order.</span></span>

>[!NOTE]
> <span data-ttu-id="5ea98-136">順序值是相對的（而不是以索引為基礎），因此可以使用任何值。</span><span class="sxs-lookup"><span data-stu-id="5ea98-136">The order value is relative (rather than index based) so any values can be used.</span></span> <span data-ttu-id="5ea98-137">例如，100和200可以接受，以取代1和2。</span><span class="sxs-lookup"><span data-stu-id="5ea98-137">For example, 100 and 200 would be acceptable in place of 1 and 2.</span></span>

``` csharp
    public class Passport
    {
        [Key]
        [Column(Order=1)]
        public int PassportNumber { get; set; }
        [Key]
        [Column(Order = 2)]
        public string IssuingCountry { get; set; }
        public DateTime Issued { get; set; }
        public DateTime Expires { get; set; }
    }
```

<span data-ttu-id="5ea98-138">如果您有具有複合外鍵的實體，則您必須指定用於對應主鍵屬性的相同資料行順序。</span><span class="sxs-lookup"><span data-stu-id="5ea98-138">If you have entities with composite foreign keys, then you must specify the same column ordering that you used for the corresponding primary key properties.</span></span>

<span data-ttu-id="5ea98-139">只有外鍵屬性中的相對順序必須相同，指派給**Order**的確切值不需要相符。</span><span class="sxs-lookup"><span data-stu-id="5ea98-139">Only the relative ordering within the foreign key properties needs to be the same, the exact values assigned to **Order** do not need to match.</span></span> <span data-ttu-id="5ea98-140">例如，在下列類別中，可以使用3和4來取代1和2。</span><span class="sxs-lookup"><span data-stu-id="5ea98-140">For example, in the following class, 3 and 4 could be used in place of 1 and 2.</span></span>

``` csharp
    public class PassportStamp
    {
        [Key]
        public int StampId { get; set; }
        public DateTime Stamped { get; set; }
        public string StampingCountry { get; set; }

        [ForeignKey("Passport")]
        [Column(Order = 1)]
        public int PassportNumber { get; set; }

        [ForeignKey("Passport")]
        [Column(Order = 2)]
        public string IssuingCountry { get; set; }

        public Passport Passport { get; set; }
    }
```

## <a name="required"></a><span data-ttu-id="5ea98-141">必要</span><span class="sxs-lookup"><span data-stu-id="5ea98-141">Required</span></span>

<span data-ttu-id="5ea98-142">必要的注釋會告訴 EF 需要特定屬性。</span><span class="sxs-lookup"><span data-stu-id="5ea98-142">The Required annotation tells EF that a particular property is required.</span></span>

<span data-ttu-id="5ea98-143">將 Required 加入 Title 屬性將會強制 EF （和 MVC），以確保屬性中有資料。</span><span class="sxs-lookup"><span data-stu-id="5ea98-143">Adding Required to the Title property will force EF (and MVC) to ensure that the property has data in it.</span></span>

``` csharp
    [Required]
    public string Title { get; set; }
```

<span data-ttu-id="5ea98-144">在應用程式中不會有任何額外的程式碼或標記變更，MVC 應用程式會執行用戶端驗證，甚至使用屬性和批註名稱動態建立訊息。</span><span class="sxs-lookup"><span data-stu-id="5ea98-144">With no additional code or markup changes in the application, an MVC application will perform client side validation, even dynamically building a message using the property and annotation names.</span></span>

![需要標題為 [建立] 頁面的錯誤](~/ef6/media/jj591583-figure02.png)

<span data-ttu-id="5ea98-146">必要的屬性也會影響所產生的資料庫，方法是讓對應的屬性成為不可為 null。</span><span class="sxs-lookup"><span data-stu-id="5ea98-146">The Required attribute will also affect the generated database by making the mapped property non-nullable.</span></span> <span data-ttu-id="5ea98-147">請注意，[標題] 欄位已變更為 [非 null]。</span><span class="sxs-lookup"><span data-stu-id="5ea98-147">Notice that the Title field has changed to “not null”.</span></span>

>[!NOTE]
> <span data-ttu-id="5ea98-148">在某些情況下，即使屬性是必要的，資料庫中的資料行還是無法是不可為 null 的。</span><span class="sxs-lookup"><span data-stu-id="5ea98-148">In some cases it may not be possible for the column in the database to be non-nullable even though the property is required.</span></span> <span data-ttu-id="5ea98-149">例如，使用多個類型的 TPH 繼承策略資料時，會儲存在單一資料表中。</span><span class="sxs-lookup"><span data-stu-id="5ea98-149">For example, when using a TPH inheritance strategy data for multiple types is stored in a single table.</span></span> <span data-ttu-id="5ea98-150">如果衍生類型包含必要的屬性，則資料行不能設為不可為 null，因為不是階層中的所有類型都有這個屬性。</span><span class="sxs-lookup"><span data-stu-id="5ea98-150">If a derived type includes a required property the column cannot be made non-nullable since not all types in the hierarchy will have this property.</span></span>

 

![Blog 資料表](~/ef6/media/jj591583-figure03.png)

 

## <a name="maxlength-and-minlength"></a><span data-ttu-id="5ea98-152">MaxLength 和 MinLength</span><span class="sxs-lookup"><span data-stu-id="5ea98-152">MaxLength and MinLength</span></span>

<span data-ttu-id="5ea98-153">MaxLength 和 MinLength 屬性可讓您指定其他屬性驗證，就如同您在必要時所做的一樣。</span><span class="sxs-lookup"><span data-stu-id="5ea98-153">The MaxLength and MinLength attributes allow you to specify additional property validations, just as you did with Required.</span></span>

<span data-ttu-id="5ea98-154">以下是長度需求的 BloggerName。</span><span class="sxs-lookup"><span data-stu-id="5ea98-154">Here is the BloggerName with length requirements.</span></span> <span data-ttu-id="5ea98-155">此範例也會示範如何結合屬性。</span><span class="sxs-lookup"><span data-stu-id="5ea98-155">The example also demonstrates how to combine attributes.</span></span>

``` csharp
    [MaxLength(10),MinLength(5)]
    public string BloggerName { get; set; }
```

<span data-ttu-id="5ea98-156">MaxLength 注釋會藉由將屬性的長度設定為10來影響資料庫。</span><span class="sxs-lookup"><span data-stu-id="5ea98-156">The MaxLength annotation will impact the database by setting the property’s length to 10.</span></span>

![顯示 BloggerName 資料行最大長度的 blog 資料表](~/ef6/media/jj591583-figure04.png)

<span data-ttu-id="5ea98-158">MVC 用戶端注釋和 EF 4.1 伺服器端批註都會接受這項驗證，再次動態建立錯誤訊息：「欄位 BloggerName 必須是字串或陣列類型，最大長度為 ' 10 '。」該訊息有點長。</span><span class="sxs-lookup"><span data-stu-id="5ea98-158">MVC client-side annotation and EF 4.1 server-side annotation will both honor this validation, again dynamically building an error message: “The field BloggerName must be a string or array type with a maximum length of '10'.” That message is a little long.</span></span> <span data-ttu-id="5ea98-159">許多批註可讓您指定具有 ErrorMessage 屬性的錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="5ea98-159">Many annotations let you specify an error message with the ErrorMessage attribute.</span></span>

``` csharp
    [MaxLength(10, ErrorMessage="BloggerName must be 10 characters or less"),MinLength(5)]
    public string BloggerName { get; set; }
```

<span data-ttu-id="5ea98-160">您也可以在必要的注釋中指定 ErrorMessage。</span><span class="sxs-lookup"><span data-stu-id="5ea98-160">You can also specify ErrorMessage in the Required annotation.</span></span>

![建立包含自訂錯誤訊息的頁面](~/ef6/media/jj591583-figure05.png)

 

## <a name="notmapped"></a><span data-ttu-id="5ea98-162">NotMapped</span><span class="sxs-lookup"><span data-stu-id="5ea98-162">NotMapped</span></span>

<span data-ttu-id="5ea98-163">Code first 慣例會指示每個支援資料類型的屬性都會在資料庫中表示。</span><span class="sxs-lookup"><span data-stu-id="5ea98-163">Code first convention dictates that every property that is of a supported data type is represented in the database.</span></span> <span data-ttu-id="5ea98-164">但在您的應用程式中，這種情況不一定如此。</span><span class="sxs-lookup"><span data-stu-id="5ea98-164">But this isn’t always the case in your applications.</span></span> <span data-ttu-id="5ea98-165">例如，您在 Blog 類別中的屬性可能會根據標題和 BloggerName 欄位建立程式碼。</span><span class="sxs-lookup"><span data-stu-id="5ea98-165">For example you might have a property in the Blog class that creates a code based on the Title and BloggerName fields.</span></span> <span data-ttu-id="5ea98-166">該屬性可以動態建立，不需要儲存。</span><span class="sxs-lookup"><span data-stu-id="5ea98-166">That property can be created dynamically and does not need to be stored.</span></span> <span data-ttu-id="5ea98-167">您可以使用 NotMapped 注釋（例如此 BlogCode 屬性），將未對應至資料庫的任何屬性標示為。</span><span class="sxs-lookup"><span data-stu-id="5ea98-167">You can mark any properties that do not map to the database with the NotMapped annotation such as this BlogCode property.</span></span>

``` csharp
    [NotMapped]
    public string BlogCode
    {
        get
        {
            return Title.Substring(0, 1) + ":" + BloggerName.Substring(0, 1);
        }
    }
```

 

## <a name="complextype"></a><span data-ttu-id="5ea98-168">ComplexType</span><span class="sxs-lookup"><span data-stu-id="5ea98-168">ComplexType</span></span>

<span data-ttu-id="5ea98-169">在一組類別中描述您的領域實體並不常見，然後將這些類別分層以描述完整的實體。</span><span class="sxs-lookup"><span data-stu-id="5ea98-169">It’s not uncommon to describe your domain entities across a set of classes and then layer those classes to describe a complete entity.</span></span> <span data-ttu-id="5ea98-170">例如，您可以將名為 BlogDetails 的類別新增至您的模型。</span><span class="sxs-lookup"><span data-stu-id="5ea98-170">For example, you may add a class called BlogDetails to your model.</span></span>

``` csharp
    public class BlogDetails
    {
        public DateTime? DateCreated { get; set; }

        [MaxLength(250)]
        public string Description { get; set; }
    }
```

<span data-ttu-id="5ea98-171">請注意，BlogDetails 沒有任何類型的索引鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="5ea98-171">Notice that BlogDetails does not have any type of key property.</span></span> <span data-ttu-id="5ea98-172">在網域導向設計中，BlogDetails 稱為「值物件」。</span><span class="sxs-lookup"><span data-stu-id="5ea98-172">In domain driven design, BlogDetails is referred to as a value object.</span></span> <span data-ttu-id="5ea98-173">Entity Framework 將值物件參考為複雜類型。</span><span class="sxs-lookup"><span data-stu-id="5ea98-173">Entity Framework refers to value objects as complex types.</span></span><span data-ttu-id="5ea98-174">  複雜類型無法自行追蹤。</span><span class="sxs-lookup"><span data-stu-id="5ea98-174">  Complex types cannot be tracked on their own.</span></span>

<span data-ttu-id="5ea98-175">不過，做為 Blog 類別中的屬性，BlogDetails 會被追蹤為 Blog 物件的一部分。</span><span class="sxs-lookup"><span data-stu-id="5ea98-175">However as a property in the Blog class, BlogDetails it will be tracked as part of a Blog object.</span></span> <span data-ttu-id="5ea98-176">為了讓 code first 能夠辨識這一點，您必須將 BlogDetails 類別標記為 ComplexType。</span><span class="sxs-lookup"><span data-stu-id="5ea98-176">In order for code first to recognize this, you must mark the BlogDetails class as a ComplexType.</span></span>

``` csharp
    [ComplexType]
    public class BlogDetails
    {
        public DateTime? DateCreated { get; set; }

        [MaxLength(250)]
        public string Description { get; set; }
    }
```

<span data-ttu-id="5ea98-177">現在您可以在 Blog 類別中新增屬性，以代表該 blog 的 BlogDetails。</span><span class="sxs-lookup"><span data-stu-id="5ea98-177">Now you can add a property in the Blog class to represent the BlogDetails for that blog.</span></span>

``` csharp
        public BlogDetails BlogDetail { get; set; }
```

<span data-ttu-id="5ea98-178">在資料庫中，Blog 資料表會包含 blog 的所有屬性，包括其 BlogDetail 屬性中包含的屬性。</span><span class="sxs-lookup"><span data-stu-id="5ea98-178">In the database, the Blog table will contain all of the properties of the blog including the properties contained in its BlogDetail property.</span></span> <span data-ttu-id="5ea98-179">根據預設，每個名稱的前面都會加上複雜型別 BlogDetail。</span><span class="sxs-lookup"><span data-stu-id="5ea98-179">By default, each one is preceded with the name of the complex type, BlogDetail.</span></span>

![具有複雜類型的 Blog 資料表](~/ef6/media/jj591583-figure06.png)


## <a name="concurrencycheck"></a><span data-ttu-id="5ea98-181">ConcurrencyCheck</span><span class="sxs-lookup"><span data-stu-id="5ea98-181">ConcurrencyCheck</span></span>

<span data-ttu-id="5ea98-182">ConcurrencyCheck 注釋可讓您在使用者編輯或刪除實體時，將一個或多個要用於資料庫並行檢查的屬性加上旗標。</span><span class="sxs-lookup"><span data-stu-id="5ea98-182">The ConcurrencyCheck annotation allows you to flag one or more properties to be used for concurrency checking in the database when a user edits or deletes an entity.</span></span> <span data-ttu-id="5ea98-183">如果您曾經使用過 EF Designer，這會與將屬性的 ConcurrencyMode 設定為 Fixed 一致。</span><span class="sxs-lookup"><span data-stu-id="5ea98-183">If you've been working with the EF Designer, this aligns with setting a property's ConcurrencyMode to Fixed.</span></span>

<span data-ttu-id="5ea98-184">讓我們來瞭解 ConcurrencyCheck 如何藉由將它新增至 BloggerName 屬性來運作。</span><span class="sxs-lookup"><span data-stu-id="5ea98-184">Let’s see how ConcurrencyCheck works by adding it to the BloggerName property.</span></span>

``` csharp
    [ConcurrencyCheck, MaxLength(10, ErrorMessage="BloggerName must be 10 characters or less"),MinLength(5)]
    public string BloggerName { get; set; }
```

<span data-ttu-id="5ea98-185">呼叫 SaveChanges 時，因為 BloggerName 欄位上的 ConcurrencyCheck 注釋，所以該屬性的原始值將會用於更新中。</span><span class="sxs-lookup"><span data-stu-id="5ea98-185">When SaveChanges is called, because of the ConcurrencyCheck annotation on the BloggerName field, the original value of that property will be used in the update.</span></span> <span data-ttu-id="5ea98-186">此命令將不只會篩選索引鍵值，同時也會針對 BloggerName 的原始值，嘗試找出正確的資料列。</span><span class="sxs-lookup"><span data-stu-id="5ea98-186">The command will attempt to locate the correct row by filtering not only on the key value but also on the original value of BloggerName.</span></span><span data-ttu-id="5ea98-187">  以下是傳送到資料庫之 UPDATE 命令的重要部分，您可以在其中看到此命令會更新具有 PrimaryTrackingKey 的資料列，以及 BloggerName 的 "Julie"，這是從資料庫抓取該 blog 時的原始值。</span><span class="sxs-lookup"><span data-stu-id="5ea98-187">  Here are the critical parts of the UPDATE command sent to the database, where you can see the command will update the row that has a PrimaryTrackingKey is 1 and a BloggerName of “Julie” which was the original value when that blog was retrieved from the database.</span></span>

``` SQL
    where (([PrimaryTrackingKey] = @4) and ([BloggerName] = @5))
    @4=1,@5=N'Julie'
```

<span data-ttu-id="5ea98-188">如果有人在此同時變更了該 blog 的博客名稱，這項更新將會失敗，而您將會收到您需要處理的 DbUpdateConcurrencyException。</span><span class="sxs-lookup"><span data-stu-id="5ea98-188">If someone has changed the blogger name for that blog in the meantime, this update will fail and you’ll get a DbUpdateConcurrencyException that you'll need to handle.</span></span>

 

## <a name="timestamp"></a><span data-ttu-id="5ea98-189">TimeStamp</span><span class="sxs-lookup"><span data-stu-id="5ea98-189">TimeStamp</span></span>

<span data-ttu-id="5ea98-190">使用 rowversion 或 timestamp 欄位進行並行檢查比較常見。</span><span class="sxs-lookup"><span data-stu-id="5ea98-190">It's more common to use rowversion or timestamp fields for concurrency checking.</span></span> <span data-ttu-id="5ea98-191">但是，您可以使用更特定的時間戳記注釋，而不是使用 ConcurrencyCheck 注釋，只要屬性的類型為位元組陣列即可。</span><span class="sxs-lookup"><span data-stu-id="5ea98-191">But rather than using the ConcurrencyCheck annotation, you can use the more specific TimeStamp annotation as long as the type of the property is byte array.</span></span> <span data-ttu-id="5ea98-192">Code first 會將 Timestamp 屬性視為與 ConcurrencyCheck 屬性相同，但它也會確保程式碼第一次產生的資料庫欄位不可為 null。</span><span class="sxs-lookup"><span data-stu-id="5ea98-192">Code first will treat Timestamp properties the same as ConcurrencyCheck properties, but it will also ensure that the database field that code first generates is non-nullable.</span></span> <span data-ttu-id="5ea98-193">在指定的類別中，您只能有一個時間戳記屬性。</span><span class="sxs-lookup"><span data-stu-id="5ea98-193">You can only have one timestamp property in a given class.</span></span>

<span data-ttu-id="5ea98-194">將下列屬性新增至 Blog 類別：</span><span class="sxs-lookup"><span data-stu-id="5ea98-194">Adding the following property to the Blog class:</span></span>

``` csharp
    [Timestamp]
    public Byte[] TimeStamp { get; set; }
```

<span data-ttu-id="5ea98-195">在程式碼中，第一次在資料庫資料表中建立不可為 null 的 timestamp 資料行。</span><span class="sxs-lookup"><span data-stu-id="5ea98-195">results in code first creating a non-nullable timestamp column in the database table.</span></span>

![包含時間戳記資料行的 blog 資料表](~/ef6/media/jj591583-figure07.png)

 

## <a name="table-and-column"></a><span data-ttu-id="5ea98-197">資料表和資料行</span><span class="sxs-lookup"><span data-stu-id="5ea98-197">Table and Column</span></span>

<span data-ttu-id="5ea98-198">如果您要讓 Code First 建立資料庫，您可能會想要變更所建立之資料表和資料行的名稱。</span><span class="sxs-lookup"><span data-stu-id="5ea98-198">If you are letting Code First create the database, you may want to change the name of the tables and columns it is creating.</span></span> <span data-ttu-id="5ea98-199">您也可以使用 Code First 搭配現有的資料庫。</span><span class="sxs-lookup"><span data-stu-id="5ea98-199">You can also use Code First with an existing database.</span></span> <span data-ttu-id="5ea98-200">但是，您的網域中的類別和屬性名稱不一定會符合資料庫中的資料表和資料行名稱。</span><span class="sxs-lookup"><span data-stu-id="5ea98-200">But it's not always the case that the names of the classes and properties in your domain match the names of the tables and columns in your database.</span></span>

<span data-ttu-id="5ea98-201">我的類別稱為 Blog，而 code first 會假設這會對應到名為 Blog 的資料表。</span><span class="sxs-lookup"><span data-stu-id="5ea98-201">My class is named Blog and by convention, code first presumes this will map to a table named Blogs.</span></span> <span data-ttu-id="5ea98-202">如果不是這種情況，您可以使用 Table 屬性來指定資料表的名稱。</span><span class="sxs-lookup"><span data-stu-id="5ea98-202">If that's not the case you can specify the name of the table with the Table attribute.</span></span> <span data-ttu-id="5ea98-203">例如，批註會指定資料表名稱是 InternalBlogs。</span><span class="sxs-lookup"><span data-stu-id="5ea98-203">Here for example, the annotation is specifying that the table name is InternalBlogs.</span></span>

``` csharp
    [Table("InternalBlogs")]
    public class Blog
```

<span data-ttu-id="5ea98-204">在指定對應資料行的屬性時，資料行注釋更為熟練。</span><span class="sxs-lookup"><span data-stu-id="5ea98-204">The Column annotation is a more adept in specifying the attributes of a mapped column.</span></span> <span data-ttu-id="5ea98-205">您可以規定 [名稱]、[資料類型]，或甚至是資料表中顯示的資料行順序。</span><span class="sxs-lookup"><span data-stu-id="5ea98-205">You can stipulate a name, data type or even the order in which a column appears in the table.</span></span> <span data-ttu-id="5ea98-206">以下是資料行屬性的範例。</span><span class="sxs-lookup"><span data-stu-id="5ea98-206">Here is an example of the Column attribute.</span></span>

``` csharp
    [Column("BlogDescription", TypeName="ntext")]
    public String Description {get;set;}
```

<span data-ttu-id="5ea98-207">請勿將資料行的 TypeName 屬性與 DataType DataAnnotation 混淆。</span><span class="sxs-lookup"><span data-stu-id="5ea98-207">Don’t confuse Column’s TypeName attribute with the DataType DataAnnotation.</span></span> <span data-ttu-id="5ea98-208">DataType 是用於 UI 的注釋，Code First 會予以忽略。</span><span class="sxs-lookup"><span data-stu-id="5ea98-208">DataType is an annotation used for the UI and is ignored by Code First.</span></span>

<span data-ttu-id="5ea98-209">以下是重新產生的資料表。</span><span class="sxs-lookup"><span data-stu-id="5ea98-209">Here is the table after it’s been regenerated.</span></span> <span data-ttu-id="5ea98-210">資料表名稱已變更為 InternalBlogs，而複雜類型的 Description 資料行現在是 BlogDescription。</span><span class="sxs-lookup"><span data-stu-id="5ea98-210">The table name has changed to InternalBlogs and Description column from the complex type is now BlogDescription.</span></span> <span data-ttu-id="5ea98-211">因為注釋中已指定名稱，所以 code first 不會使用以複雜型別名稱來啟動資料行名稱的慣例。</span><span class="sxs-lookup"><span data-stu-id="5ea98-211">Because the name was specified in the annotation, code first will not use the convention of starting the column name with the name of the complex type.</span></span>

![已重新命名的 blog 資料表和資料行](~/ef6/media/jj591583-figure08.png)

 

## <a name="databasegenerated"></a><span data-ttu-id="5ea98-213">DatabaseGenerated</span><span class="sxs-lookup"><span data-stu-id="5ea98-213">DatabaseGenerated</span></span>

<span data-ttu-id="5ea98-214">重要的資料庫功能是具有計算屬性的能力。</span><span class="sxs-lookup"><span data-stu-id="5ea98-214">An important database features is the ability to have computed properties.</span></span> <span data-ttu-id="5ea98-215">如果您要將 Code First 類別對應至包含計算資料行的資料表，您不會想要 Entity Framework 嘗試更新這些資料行。</span><span class="sxs-lookup"><span data-stu-id="5ea98-215">If you're mapping your Code First classes to tables that contain computed columns, you don't want Entity Framework to try to update those columns.</span></span> <span data-ttu-id="5ea98-216">但是，您可以在插入或更新資料之後，讓 EF 從資料庫傳回這些值。</span><span class="sxs-lookup"><span data-stu-id="5ea98-216">But you do want EF to return those values from the database after you've inserted or updated data.</span></span> <span data-ttu-id="5ea98-217">您可以使用 DatabaseGenerated 注釋，將類別中的屬性和計算的列舉一併加上旗標。</span><span class="sxs-lookup"><span data-stu-id="5ea98-217">You can use the DatabaseGenerated annotation to flag those properties in your class along with the Computed enum.</span></span> <span data-ttu-id="5ea98-218">其他列舉為 None 和 Identity。</span><span class="sxs-lookup"><span data-stu-id="5ea98-218">Other enums are None and Identity.</span></span>

``` csharp
    [DatabaseGenerated(DatabaseGeneratedOption.Computed)]
    public DateTime DateCreated { get; set; }
```

<span data-ttu-id="5ea98-219">當程式碼第一次產生資料庫時，您可以使用在 byte 或 timestamp 資料行上產生的資料庫，否則，您應該只在指向現有的資料庫時才使用它，因為程式碼 first 無法判斷計算資料行的公式。</span><span class="sxs-lookup"><span data-stu-id="5ea98-219">You can use database generated on byte or timestamp columns when code first is generating the database, otherwise you should only use this when pointing to existing databases because code first won't be able to determine the formula for the computed column.</span></span>

<span data-ttu-id="5ea98-220">您會在上面閱讀，根據預設，做為整數的索引鍵屬性會成為資料庫中的識別索引鍵。</span><span class="sxs-lookup"><span data-stu-id="5ea98-220">You read above that by default, a key property that is an integer will become an identity key in the database.</span></span> <span data-ttu-id="5ea98-221">也就是將 DatabaseGenerated 設定為 DatabaseGeneratedOption。</span><span class="sxs-lookup"><span data-stu-id="5ea98-221">That would be the same as setting DatabaseGenerated to DatabaseGeneratedOption.Identity.</span></span> <span data-ttu-id="5ea98-222">如果您不想讓它成為身分識別索引鍵，您可以將值設定為 DatabaseGeneratedOption。</span><span class="sxs-lookup"><span data-stu-id="5ea98-222">If you do not want it to be an identity key, you can set the value to DatabaseGeneratedOption.None.</span></span>

 

## <a name="index"></a><span data-ttu-id="5ea98-223">索引</span><span class="sxs-lookup"><span data-stu-id="5ea98-223">Index</span></span>

> [!NOTE]
> <span data-ttu-id="5ea98-224">**Ef 6.1 僅適用**于 Entity Framework 6.1 中引進的索引屬性。</span><span class="sxs-lookup"><span data-stu-id="5ea98-224">**EF6.1 Onwards Only** - The Index attribute was introduced in Entity Framework 6.1.</span></span> <span data-ttu-id="5ea98-225">如果您使用較舊的版本，本節中的資訊將不適用。</span><span class="sxs-lookup"><span data-stu-id="5ea98-225">If you are using an earlier version the information in this section does not apply.</span></span>

<span data-ttu-id="5ea98-226">您可以使用**IndexAttribute**，在一或多個資料行上建立索引。</span><span class="sxs-lookup"><span data-stu-id="5ea98-226">You can create an index on one or more columns using the **IndexAttribute**.</span></span> <span data-ttu-id="5ea98-227">將屬性加入至一個或多個屬性，會使 EF 在建立資料庫時于資料庫中建立對應的索引，或 scaffold 對應的**CreateIndex**呼叫（如果您使用 Code First 移轉）。</span><span class="sxs-lookup"><span data-stu-id="5ea98-227">Adding the attribute to one or more properties will cause EF to create the corresponding index in the database when it creates the database, or scaffold the corresponding **CreateIndex** calls if you are using Code First Migrations.</span></span>

<span data-ttu-id="5ea98-228">例如，下列程式碼會在資料庫的 [**貼**文] 資料表的 [**評**等] 資料行上建立索引。</span><span class="sxs-lookup"><span data-stu-id="5ea98-228">For example, the following code will result in an index being created on the **Rating** column of the **Posts** table in the database.</span></span>

``` csharp
    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }
        [Index]
        public int Rating { get; set; }
        public int BlogId { get; set; }
    }
```

<span data-ttu-id="5ea98-229">根據預設，索引會命名為**IX\_&lt;屬性名稱&gt;** （在上述範例中為 IX\_分級）。</span><span class="sxs-lookup"><span data-stu-id="5ea98-229">By default, the index will be named **IX\_&lt;property name&gt;** (IX\_Rating in the above example).</span></span> <span data-ttu-id="5ea98-230">不過，您也可以指定索引的名稱。</span><span class="sxs-lookup"><span data-stu-id="5ea98-230">You can also specify a name for the index though.</span></span> <span data-ttu-id="5ea98-231">下列範例會指定索引應該命名為**PostRatingIndex**。</span><span class="sxs-lookup"><span data-stu-id="5ea98-231">The following example specifies that the index should be named **PostRatingIndex**.</span></span>

``` csharp
    [Index("PostRatingIndex")]
    public int Rating { get; set; }
```

<span data-ttu-id="5ea98-232">根據預設，索引不是唯一的，但您可以使用**IsUnique**具名引數來指定索引應該是唯一的。</span><span class="sxs-lookup"><span data-stu-id="5ea98-232">By default, indexes are non-unique, but you can use the **IsUnique** named parameter to specify that an index should be unique.</span></span> <span data-ttu-id="5ea98-233">下列範例會在**使用者**的登入名稱上引進唯一索引。</span><span class="sxs-lookup"><span data-stu-id="5ea98-233">The following example introduces a unique index on a **User**'s login name.</span></span>

``` csharp
    public class User
    {
        public int UserId { get; set; }

        [Index(IsUnique = true)]
        [StringLength(200)]
        public string Username { get; set; }

        public string DisplayName { get; set; }
    }
```

### <a name="multiple-column-indexes"></a><span data-ttu-id="5ea98-234">多重資料行索引</span><span class="sxs-lookup"><span data-stu-id="5ea98-234">Multiple-Column Indexes</span></span>

<span data-ttu-id="5ea98-235">跨越多個資料行的索引是使用相同的名稱，在指定資料表的多個索引注釋中指定。</span><span class="sxs-lookup"><span data-stu-id="5ea98-235">Indexes that span multiple columns are specified by using the same name in multiple Index annotations for a given table.</span></span> <span data-ttu-id="5ea98-236">當您建立多重資料行索引時，您必須指定索引中資料行的順序。</span><span class="sxs-lookup"><span data-stu-id="5ea98-236">When you create multi-column indexes, you need to specify an order for the columns in the index.</span></span> <span data-ttu-id="5ea98-237">例如，下列程式碼會在**分級**和**BlogId**上建立多個資料行的索引，稱為**IX\_BlogIdAndRating**。</span><span class="sxs-lookup"><span data-stu-id="5ea98-237">For example, the following code creates a multi-column index on **Rating** and **BlogId** called **IX\_BlogIdAndRating**.</span></span> <span data-ttu-id="5ea98-238">**BlogId**是索引中的第一個資料行，而**評**等是第二個數據行。</span><span class="sxs-lookup"><span data-stu-id="5ea98-238">**BlogId** is the first column in the index and **Rating** is the second.</span></span>

``` csharp
    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }
        [Index("IX_BlogIdAndRating", 2)]
        public int Rating { get; set; }
        [Index("IX_BlogIdAndRating", 1)]
        public int BlogId { get; set; }
    }
```

 

## <a name="relationship-attributes-inverseproperty-and-foreignkey"></a><span data-ttu-id="5ea98-239">關聯性屬性： InverseProperty 和 ForeignKey</span><span class="sxs-lookup"><span data-stu-id="5ea98-239">Relationship Attributes: InverseProperty and ForeignKey</span></span>

> [!NOTE]
> <span data-ttu-id="5ea98-240">此頁面提供使用資料批註在 Code First 模型中設定關聯性的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="5ea98-240">This page provides information about setting up relationships in your Code First model using Data Annotations.</span></span> <span data-ttu-id="5ea98-241">如需 EF 中的關聯性以及如何使用關聯性存取和運算元據的一般資訊，請參閱[& 導覽屬性的關聯](~/ef6/fundamentals/relationships.md)性。 \*</span><span class="sxs-lookup"><span data-stu-id="5ea98-241">For general information about relationships in EF and how to access and manipulate data using relationships, see [Relationships & Navigation Properties](~/ef6/fundamentals/relationships.md).\*</span></span>

<span data-ttu-id="5ea98-242">Code first 慣例會負責您模型中最常見的關聯性，但在某些情況下，它需要協助。</span><span class="sxs-lookup"><span data-stu-id="5ea98-242">Code first convention will take care of the most common relationships in your model, but there are some cases where it needs help.</span></span>

<span data-ttu-id="5ea98-243">變更 Blog 類別中的索引鍵屬性名稱，會建立與 Post 關聯的問題。</span><span class="sxs-lookup"><span data-stu-id="5ea98-243">Changing the name of the key property in the Blog class created a problem with its relationship to Post.</span></span> 

<span data-ttu-id="5ea98-244">產生資料庫時，code first 會看到 Post 類別中的 BlogId 屬性，並依照其符合類別名稱加上 "Id" 的慣例來辨識它，作為 Blog 類別的外鍵。</span><span class="sxs-lookup"><span data-stu-id="5ea98-244">When generating the database, code first sees the BlogId property in the Post class and recognizes it, by the convention that it matches a class name plus “Id”, as a foreign key to the Blog class.</span></span> <span data-ttu-id="5ea98-245">但在 blog 類別中沒有 BlogId 屬性。</span><span class="sxs-lookup"><span data-stu-id="5ea98-245">But there is no BlogId property in the blog class.</span></span> <span data-ttu-id="5ea98-246">其解決方法是在 Post 中建立導覽屬性，並使用外部 DataAnnotation 協助程式碼先瞭解如何使用 BlogId 屬性來建立兩個類別之間的關聯性，以及如何在中指定條件約束。資料.</span><span class="sxs-lookup"><span data-stu-id="5ea98-246">The solution for this is to create a navigation property in the Post and use the Foreign DataAnnotation to help code first understand how to build the relationship between the two classes —using the Post.BlogId property — as well as how to specify constraints in the database.</span></span>

``` csharp
    public class Post
    {
            public int Id { get; set; }
            public string Title { get; set; }
            public DateTime DateCreated { get; set; }
            public string Content { get; set; }
            public int BlogId { get; set; }
            [ForeignKey("BlogId")]
            public Blog Blog { get; set; }
            public ICollection<Comment> Comments { get; set; }
    }
```

<span data-ttu-id="5ea98-247">資料庫中的條件約束會顯示 InternalBlogs PrimaryTrackingKey 與 BlogId 之間的關聯性。</span><span class="sxs-lookup"><span data-stu-id="5ea98-247">The constraint in the database shows a relationship between InternalBlogs.PrimaryTrackingKey and Posts.BlogId.</span></span> 

![InternalBlogs 之間的關聯性 PrimaryTrackingKey 和貼文。 BlogId](~/ef6/media/jj591583-figure09.png)

<span data-ttu-id="5ea98-249">當您的類別之間有多個關聯性時，就會使用 InverseProperty。</span><span class="sxs-lookup"><span data-stu-id="5ea98-249">The InverseProperty is used when you have multiple relationships between classes.</span></span>

<span data-ttu-id="5ea98-250">在 Post 類別中，您可能想要追蹤誰寫了 blog 文章以及編輯者。</span><span class="sxs-lookup"><span data-stu-id="5ea98-250">In the Post class, you may want to keep track of who wrote a blog post as well as who edited it.</span></span> <span data-ttu-id="5ea98-251">以下是 Post 類別的兩個新導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="5ea98-251">Here are two new navigation properties for the Post class.</span></span>

``` csharp
    public Person CreatedBy { get; set; }
    public Person UpdatedBy { get; set; }
```

<span data-ttu-id="5ea98-252">您也必須在這些屬性所參考的 Person 類別中加入。</span><span class="sxs-lookup"><span data-stu-id="5ea98-252">You’ll also need to add in the Person class referenced by these properties.</span></span> <span data-ttu-id="5ea98-253">Person 類別具有回到文章的導覽屬性，一個用於個人所撰寫的所有貼文，另一個則用於該人員所更新的所有文章。</span><span class="sxs-lookup"><span data-stu-id="5ea98-253">The Person class has navigation properties back to the Post, one for all of the posts written by the person and one for all of the posts updated by that person.</span></span>

``` csharp
    public class Person
    {
            public int Id { get; set; }
            public string Name { get; set; }
            public List<Post> PostsWritten { get; set; }
            public List<Post> PostsUpdated { get; set; }
    }
```

<span data-ttu-id="5ea98-254">Code first 無法單獨比對這兩個類別中的屬性。</span><span class="sxs-lookup"><span data-stu-id="5ea98-254">Code first is not able to match up the properties in the two classes on its own.</span></span> <span data-ttu-id="5ea98-255">貼文的資料庫資料表應該有一個 CreatedBy 人員的外鍵，另一個用於 UpdatedBy 人員，但 code first 會建立四個外鍵屬性： Person\_Id、Person\_Id1、CreatedBy\_Id 和 UpdatedBy\_Id。</span><span class="sxs-lookup"><span data-stu-id="5ea98-255">The database table for Posts should have one foreign key for the CreatedBy person and one for the UpdatedBy person but code first will create four foreign key properties: Person\_Id, Person\_Id1, CreatedBy\_Id and UpdatedBy\_Id.</span></span>

![附有額外外鍵的貼文資料表](~/ef6/media/jj591583-figure10.png)

<span data-ttu-id="5ea98-257">若要修正這些問題，您可以使用 InverseProperty 批註來指定屬性的對齊方式。</span><span class="sxs-lookup"><span data-stu-id="5ea98-257">To fix these problems, you can use the InverseProperty annotation to specify the alignment of the properties.</span></span>

``` csharp
    [InverseProperty("CreatedBy")]
    public List<Post> PostsWritten { get; set; }

    [InverseProperty("UpdatedBy")]
    public List<Post> PostsUpdated { get; set; }
```

<span data-ttu-id="5ea98-258">因為 Person 中的 PostsWritten 屬性知道這會參考 Post 類型，所以它會建立 CreatedBy 的關聯性。</span><span class="sxs-lookup"><span data-stu-id="5ea98-258">Because the PostsWritten property in Person knows that this refers to the Post type, it will build the relationship to Post.CreatedBy.</span></span> <span data-ttu-id="5ea98-259">同樣地，PostsUpdated 會連接到 UpdatedBy。</span><span class="sxs-lookup"><span data-stu-id="5ea98-259">Similarly, PostsUpdated will be connected to Post.UpdatedBy.</span></span> <span data-ttu-id="5ea98-260">而 code first 不會建立額外的外鍵。</span><span class="sxs-lookup"><span data-stu-id="5ea98-260">And code first will not create the extra foreign keys.</span></span>

![沒有額外外鍵的貼文資料表](~/ef6/media/jj591583-figure11.png)

 

## <a name="summary"></a><span data-ttu-id="5ea98-262">摘要</span><span class="sxs-lookup"><span data-stu-id="5ea98-262">Summary</span></span>

<span data-ttu-id="5ea98-263">DataAnnotations 不僅可讓您在程式碼的第一個類別中描述用戶端和伺服器端驗證，還可讓您增強，甚至更正程式碼第一次將根據其慣例來對類別進行的假設。</span><span class="sxs-lookup"><span data-stu-id="5ea98-263">DataAnnotations not only let you describe client and server side validation in your code first classes, but they also allow you to enhance and even correct the assumptions that code first will make about your classes based on its conventions.</span></span> <span data-ttu-id="5ea98-264">有了 DataAnnotations，您不僅可以驅動資料庫架構的產生，還可以將程式碼的第一個類別對應到既有的資料庫。</span><span class="sxs-lookup"><span data-stu-id="5ea98-264">With DataAnnotations you can not only drive database schema generation, but you can also map your code first classes to a pre-existing database.</span></span>

<span data-ttu-id="5ea98-265">雖然它們非常有彈性，但請記住，DataAnnotations 只會提供您在程式碼第一個類別上可以進行的最常需要的設定變更。</span><span class="sxs-lookup"><span data-stu-id="5ea98-265">While they are very flexible, keep in mind that DataAnnotations provide only the most commonly needed configuration changes you can make on your code first classes.</span></span> <span data-ttu-id="5ea98-266">若要針對某些邊緣案例設定您的類別，您應該查看其他設定機制，Code First 的流暢 API。</span><span class="sxs-lookup"><span data-stu-id="5ea98-266">To configure your classes for some of the edge cases, you should look to the alternate configuration mechanism, Code First’s Fluent API .</span></span>
