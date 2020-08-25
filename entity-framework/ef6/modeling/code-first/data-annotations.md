---
title: Code First 資料批註-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 80abefbd-23c9-4fce-9cd3-520e5df9856e
ms.openlocfilehash: 17370b83aee9974161b1771e8a1dc99c2de27f0f
ms.sourcegitcommit: 6f7af3f138bf7c724cbdda261f97e5cf7035e8d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/25/2020
ms.locfileid: "88847614"
---
# <a name="code-first-data-annotations"></a><span data-ttu-id="12932-102">Code First 資料註解</span><span class="sxs-lookup"><span data-stu-id="12932-102">Code First Data Annotations</span></span>
> [!NOTE]
> <span data-ttu-id="12932-103">**僅限 ef 4.1** ：此頁面中所討論的功能、api 等已于 Entity Framework 4.1 中引進。</span><span class="sxs-lookup"><span data-stu-id="12932-103">**EF4.1 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 4.1.</span></span> <span data-ttu-id="12932-104">如果您使用的是較舊的版本，則不適用部分或全部資訊。</span><span class="sxs-lookup"><span data-stu-id="12932-104">If you are using an earlier version, some or all of this information does not apply.</span></span>

<span data-ttu-id="12932-105">此頁面上的內容是由 Julie Lerman () 最初撰寫的文章所調整 \<http://thedatafarm.com> 。</span><span class="sxs-lookup"><span data-stu-id="12932-105">The content on this page is adapted from an article originally written by Julie Lerman (\<http://thedatafarm.com>).</span></span>

<span data-ttu-id="12932-106">Entity Framework Code First 可讓您使用自己的網域類別來代表 EF 依賴來執行查詢、變更追蹤和更新函數的模型。</span><span class="sxs-lookup"><span data-stu-id="12932-106">Entity Framework Code First allows you to use your own domain classes to represent the model that EF relies on to perform querying, change tracking, and updating functions.</span></span> <span data-ttu-id="12932-107">Code First 會利用稱為「慣例 over 設定」的程式設計模式。</span><span class="sxs-lookup"><span data-stu-id="12932-107">Code First leverages a programming pattern referred to as 'convention over configuration.'</span></span> <span data-ttu-id="12932-108">Code First 將假設您的類別遵循 Entity Framework 的慣例，在這種情況下，將會自動瞭解如何執行其工作。</span><span class="sxs-lookup"><span data-stu-id="12932-108">Code First will assume that your classes follow the conventions of Entity Framework, and in that case, will automatically work out how to perform its job.</span></span> <span data-ttu-id="12932-109">但是，如果您的類別未遵循這些慣例，您可以將設定新增至您的類別，以提供 EF 具有必要的資訊。</span><span class="sxs-lookup"><span data-stu-id="12932-109">However, if your classes do not follow those conventions, you have the ability to add configurations to your classes to provide EF with the requisite information.</span></span>

<span data-ttu-id="12932-110">Code First 提供兩種方式，將這些設定新增至您的類別。</span><span class="sxs-lookup"><span data-stu-id="12932-110">Code First gives you two ways to add these configurations to your classes.</span></span> <span data-ttu-id="12932-111">其中一個是使用簡單的屬性（稱為 DataAnnotations），第二個則是使用 Code First 的流暢 API，可讓您以命令方式在程式碼中描述設定。</span><span class="sxs-lookup"><span data-stu-id="12932-111">One is using simple attributes called DataAnnotations, and the second is using Code First’s Fluent API, which provides you with a way to describe configurations imperatively, in code.</span></span>

<span data-ttu-id="12932-112">本文將著重于使用 DataAnnotations 命名空間中的 DataAnnotations () 設定您的類別–強調最常需要的設定。</span><span class="sxs-lookup"><span data-stu-id="12932-112">This article will focus on using DataAnnotations (in the System.ComponentModel.DataAnnotations namespace) to configure your classes – highlighting the most commonly needed configurations.</span></span> <span data-ttu-id="12932-113">許多 .NET 應用程式（例如 ASP.NET MVC）也會瞭解 DataAnnotations，這類應用程式可讓這些應用程式利用相同的注釋來進行用戶端驗證。</span><span class="sxs-lookup"><span data-stu-id="12932-113">DataAnnotations are also understood by a number of .NET applications, such as ASP.NET MVC which allows these applications to leverage the same annotations for client-side validations.</span></span>


## <a name="the-model"></a><span data-ttu-id="12932-114">模型</span><span class="sxs-lookup"><span data-stu-id="12932-114">The model</span></span>

<span data-ttu-id="12932-115">我將示範如何使用一組簡單的類別 Code First DataAnnotations： Blog 和 Post。</span><span class="sxs-lookup"><span data-stu-id="12932-115">I’ll demonstrate Code First DataAnnotations with a simple pair of classes: Blog and Post.</span></span>

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

<span data-ttu-id="12932-116">如同這些，Blog 和 Post 類別可方便您遵循 code first 慣例，而不需要調整以啟用 EF 相容性。</span><span class="sxs-lookup"><span data-stu-id="12932-116">As they are, the Blog and Post classes conveniently follow code first convention and require no tweaks to enable EF compatability.</span></span> <span data-ttu-id="12932-117">不過，您也可以使用注釋，將有關類別和其所對應之資料庫的詳細資訊提供給 EF。</span><span class="sxs-lookup"><span data-stu-id="12932-117">However, you can also use the annotations to provide more information to EF about the classes and the database to which they map.</span></span>

 

## <a name="key"></a><span data-ttu-id="12932-118">答案</span><span class="sxs-lookup"><span data-stu-id="12932-118">Key</span></span>

<span data-ttu-id="12932-119">Entity Framework 依賴每個實體具有用於實體追蹤的索引鍵值。</span><span class="sxs-lookup"><span data-stu-id="12932-119">Entity Framework relies on every entity having a key value that is used for entity tracking.</span></span> <span data-ttu-id="12932-120">Code First 的其中一個慣例是隱含索引鍵屬性;Code First 會尋找名為 "Id" 的屬性，或類別名稱和 "Id" 的組合，例如 "BlogId"。</span><span class="sxs-lookup"><span data-stu-id="12932-120">One convention of Code First is implicit key properties; Code First will look for a property named “Id”, or a combination of class name and “Id”, such as “BlogId”.</span></span> <span data-ttu-id="12932-121">這個屬性會對應至資料庫中的主鍵資料行。</span><span class="sxs-lookup"><span data-stu-id="12932-121">This property will map to a primary key column in the database.</span></span>

<span data-ttu-id="12932-122">Blog 和 Post 類別都遵循此慣例。</span><span class="sxs-lookup"><span data-stu-id="12932-122">The Blog and Post classes both follow this convention.</span></span> <span data-ttu-id="12932-123">如果沒有，該怎麼辦？</span><span class="sxs-lookup"><span data-stu-id="12932-123">What if they didn’t?</span></span> <span data-ttu-id="12932-124">如果 Blog 使用名稱 *PrimaryTrackingKey* ，或甚至是 *foo*？</span><span class="sxs-lookup"><span data-stu-id="12932-124">What if Blog used the name *PrimaryTrackingKey* instead, or even *foo*?</span></span> <span data-ttu-id="12932-125">如果 code first 找不到符合這個慣例的屬性，就會擲回例外狀況，因為 Entity Framework 必須有索引鍵屬性的需求。</span><span class="sxs-lookup"><span data-stu-id="12932-125">If code first does not find a property that matches this convention it will throw an exception because of Entity Framework’s requirement that you must have a key property.</span></span> <span data-ttu-id="12932-126">您可以使用索引鍵批註來指定要使用哪個屬性作為 EntityKey。</span><span class="sxs-lookup"><span data-stu-id="12932-126">You can use the key annotation to specify which property is to be used as the EntityKey.</span></span>

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

<span data-ttu-id="12932-127">如果您使用 code first 的資料庫產生功能，則 Blog 資料表將會有一個名為 PrimaryTrackingKey 的主鍵資料行，此資料行也預設定義為身分識別。</span><span class="sxs-lookup"><span data-stu-id="12932-127">If you are using code first’s database generation feature, the Blog table will have a primary key column named PrimaryTrackingKey, which is also defined as Identity by default.</span></span>

![具有主鍵的 Blog 資料表](~/ef6/media/jj591583-figure01.png)

### <a name="composite-keys"></a><span data-ttu-id="12932-129">複合索引鍵</span><span class="sxs-lookup"><span data-stu-id="12932-129">Composite keys</span></span>

<span data-ttu-id="12932-130">Entity Framework 支援複合索引鍵-包含一個以上屬性的主鍵。</span><span class="sxs-lookup"><span data-stu-id="12932-130">Entity Framework supports composite keys - primary keys that consist of more than one property.</span></span> <span data-ttu-id="12932-131">例如，您可能有一個 Passport 類別，其主鍵是 PassportNumber 和 IssuingCountry 的組合。</span><span class="sxs-lookup"><span data-stu-id="12932-131">For example, you could have a Passport class whose primary key is a combination of PassportNumber and IssuingCountry.</span></span>

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

<span data-ttu-id="12932-132">嘗試在 EF 模型中使用上述類別會導致 `InvalidOperationException` ：</span><span class="sxs-lookup"><span data-stu-id="12932-132">Attempting to use the above class in your EF model would result in an `InvalidOperationException`:</span></span>

<span data-ttu-id="12932-133">*無法判斷類型 ' Passport ' 的複合主要索引鍵順序。使用 ColumnAttribute 或 HasKey 方法來指定複合主鍵的順序。*</span><span class="sxs-lookup"><span data-stu-id="12932-133">*Unable to determine composite primary key ordering for type 'Passport'. Use the ColumnAttribute or the HasKey method to specify an order for composite primary keys.*</span></span>

<span data-ttu-id="12932-134">若要使用複合索引鍵，Entity Framework 必須定義索引鍵屬性的順序。</span><span class="sxs-lookup"><span data-stu-id="12932-134">In order to use composite keys, Entity Framework requires you to define an order for the key properties.</span></span> <span data-ttu-id="12932-135">您可以使用資料行批註來指定順序，來進行這項作業。</span><span class="sxs-lookup"><span data-stu-id="12932-135">You can do this by using the Column annotation to specify an order.</span></span>

>[!NOTE]
> <span data-ttu-id="12932-136">順序值是相對 (而不是以索引為基礎) 因此可以使用任何值。</span><span class="sxs-lookup"><span data-stu-id="12932-136">The order value is relative (rather than index based) so any values can be used.</span></span> <span data-ttu-id="12932-137">例如，可以接受100和200來取代1和2。</span><span class="sxs-lookup"><span data-stu-id="12932-137">For example, 100 and 200 would be acceptable in place of 1 and 2.</span></span>

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

<span data-ttu-id="12932-138">如果您有具有複合外鍵的實體，則必須指定用於對應主鍵屬性的相同資料行排序。</span><span class="sxs-lookup"><span data-stu-id="12932-138">If you have entities with composite foreign keys, then you must specify the same column ordering that you used for the corresponding primary key properties.</span></span>

<span data-ttu-id="12932-139">只有外鍵屬性內的相對順序必須相同，指派給 **Order** 的確切值不需要相符。</span><span class="sxs-lookup"><span data-stu-id="12932-139">Only the relative ordering within the foreign key properties needs to be the same, the exact values assigned to **Order** do not need to match.</span></span> <span data-ttu-id="12932-140">例如，在下列類別中，3和4可以用來取代1和2。</span><span class="sxs-lookup"><span data-stu-id="12932-140">For example, in the following class, 3 and 4 could be used in place of 1 and 2.</span></span>

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

## <a name="required"></a><span data-ttu-id="12932-141">必要</span><span class="sxs-lookup"><span data-stu-id="12932-141">Required</span></span>

<span data-ttu-id="12932-142">`Required`批註會告知 EF 需要特定的屬性。</span><span class="sxs-lookup"><span data-stu-id="12932-142">The `Required` annotation tells EF that a particular property is required.</span></span>

<span data-ttu-id="12932-143">將必要專案加入 Title 屬性會強制 EF (和 MVC) ，以確保屬性中有資料。</span><span class="sxs-lookup"><span data-stu-id="12932-143">Adding Required to the Title property will force EF (and MVC) to ensure that the property has data in it.</span></span>

``` csharp
    [Required]
    public string Title { get; set; }
```

<span data-ttu-id="12932-144">在應用程式中沒有任何額外的程式碼或標記變更時，MVC 應用程式會執行用戶端驗證，甚至是使用屬性和批註名稱動態建立訊息。</span><span class="sxs-lookup"><span data-stu-id="12932-144">With no additional code or markup changes in the application, an MVC application will perform client side validation, even dynamically building a message using the property and annotation names.</span></span>

![具有標題的建立頁面必須有錯誤](~/ef6/media/jj591583-figure02.png)

<span data-ttu-id="12932-146">必要屬性也會將對應的屬性設為不可為 null，進而影響產生的資料庫。</span><span class="sxs-lookup"><span data-stu-id="12932-146">The Required attribute will also affect the generated database by making the mapped property non-nullable.</span></span> <span data-ttu-id="12932-147">請注意，[標題] 欄位已變更為 [不是 null]。</span><span class="sxs-lookup"><span data-stu-id="12932-147">Notice that the Title field has changed to “not null”.</span></span>

>[!NOTE]
> <span data-ttu-id="12932-148">在某些情況下，即使屬性是必要的，資料庫中的資料行還是可能無法成為可為 null。</span><span class="sxs-lookup"><span data-stu-id="12932-148">In some cases it may not be possible for the column in the database to be non-nullable even though the property is required.</span></span> <span data-ttu-id="12932-149">例如，使用多個類型的 TPH 繼承策略資料時，會儲存在單一資料表中。</span><span class="sxs-lookup"><span data-stu-id="12932-149">For example, when using a TPH inheritance strategy data for multiple types is stored in a single table.</span></span> <span data-ttu-id="12932-150">如果衍生型別包含必要的屬性，則資料行不能成為不可為 null，因為階層中的所有類型都不會有這個屬性。</span><span class="sxs-lookup"><span data-stu-id="12932-150">If a derived type includes a required property the column cannot be made non-nullable since not all types in the hierarchy will have this property.</span></span>

 

![Blog 資料表](~/ef6/media/jj591583-figure03.png)

 

## <a name="maxlength-and-minlength"></a><span data-ttu-id="12932-152">MaxLength 和 MinLength</span><span class="sxs-lookup"><span data-stu-id="12932-152">MaxLength and MinLength</span></span>

<span data-ttu-id="12932-153">`MaxLength`和 `MinLength` 屬性可讓您指定其他屬性驗證，就像您使用的一樣 `Required` 。</span><span class="sxs-lookup"><span data-stu-id="12932-153">The `MaxLength` and `MinLength` attributes allow you to specify additional property validations, just as you did with `Required`.</span></span>

<span data-ttu-id="12932-154">以下是長度需求的 BloggerName。</span><span class="sxs-lookup"><span data-stu-id="12932-154">Here is the BloggerName with length requirements.</span></span> <span data-ttu-id="12932-155">此範例也會示範如何結合屬性。</span><span class="sxs-lookup"><span data-stu-id="12932-155">The example also demonstrates how to combine attributes.</span></span>

``` csharp
    [MaxLength(10),MinLength(5)]
    public string BloggerName { get; set; }
```

<span data-ttu-id="12932-156">MaxLength 注釋會將屬性的長度設定為10，以影響資料庫。</span><span class="sxs-lookup"><span data-stu-id="12932-156">The MaxLength annotation will impact the database by setting the property’s length to 10.</span></span>

![顯示 BloggerName 資料行最大長度的 blog 資料表](~/ef6/media/jj591583-figure04.png)

<span data-ttu-id="12932-158">MVC 用戶端注釋和 EF 4.1 伺服器端注釋都會接受這項驗證，再次動態建立錯誤訊息：「欄位 BloggerName 必須是最大長度為 ' 10 ' 的字串或陣列類型。」該訊息很長。</span><span class="sxs-lookup"><span data-stu-id="12932-158">MVC client-side annotation and EF 4.1 server-side annotation will both honor this validation, again dynamically building an error message: “The field BloggerName must be a string or array type with a maximum length of '10'.” That message is a little long.</span></span> <span data-ttu-id="12932-159">許多批註可讓您使用 ErrorMessage 屬性來指定錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="12932-159">Many annotations let you specify an error message with the ErrorMessage attribute.</span></span>

``` csharp
    [MaxLength(10, ErrorMessage="BloggerName must be 10 characters or less"),MinLength(5)]
    public string BloggerName { get; set; }
```

<span data-ttu-id="12932-160">您也可以在必要的批註中指定 ErrorMessage。</span><span class="sxs-lookup"><span data-stu-id="12932-160">You can also specify ErrorMessage in the Required annotation.</span></span>

![具有自訂錯誤訊息的 [建立] 頁面](~/ef6/media/jj591583-figure05.png)

 

## <a name="notmapped"></a><span data-ttu-id="12932-162">NotMapped</span><span class="sxs-lookup"><span data-stu-id="12932-162">NotMapped</span></span>

<span data-ttu-id="12932-163">Code first 慣例規定在資料庫中表示支援的資料類型的每個屬性。</span><span class="sxs-lookup"><span data-stu-id="12932-163">Code first convention dictates that every property that is of a supported data type is represented in the database.</span></span> <span data-ttu-id="12932-164">但在您的應用程式中，這不一定是如此。</span><span class="sxs-lookup"><span data-stu-id="12932-164">But this isn’t always the case in your applications.</span></span> <span data-ttu-id="12932-165">例如，您的 Blog 類別中可能會有一個以標題和 BloggerName 欄位為基礎建立程式碼的屬性。</span><span class="sxs-lookup"><span data-stu-id="12932-165">For example you might have a property in the Blog class that creates a code based on the Title and BloggerName fields.</span></span> <span data-ttu-id="12932-166">該屬性可以動態建立，且不需要儲存。</span><span class="sxs-lookup"><span data-stu-id="12932-166">That property can be created dynamically and does not need to be stored.</span></span> <span data-ttu-id="12932-167">您可以使用 NotMapped 批註（例如這個 BlogCode 屬性），將任何未對應到資料庫的屬性標示為。</span><span class="sxs-lookup"><span data-stu-id="12932-167">You can mark any properties that do not map to the database with the NotMapped annotation such as this BlogCode property.</span></span>

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

 

## <a name="complextype"></a><span data-ttu-id="12932-168">ComplexType</span><span class="sxs-lookup"><span data-stu-id="12932-168">ComplexType</span></span>

<span data-ttu-id="12932-169">在一組類別之間描述您的領域實體，然後將這些類別分層以描述完整的實體並不常見。</span><span class="sxs-lookup"><span data-stu-id="12932-169">It’s not uncommon to describe your domain entities across a set of classes and then layer those classes to describe a complete entity.</span></span> <span data-ttu-id="12932-170">例如，您可以將名為 BlogDetails 的類別新增至您的模型。</span><span class="sxs-lookup"><span data-stu-id="12932-170">For example, you may add a class called BlogDetails to your model.</span></span>

``` csharp
    public class BlogDetails
    {
        public DateTime? DateCreated { get; set; }

        [MaxLength(250)]
        public string Description { get; set; }
    }
```

<span data-ttu-id="12932-171">請注意，沒有 `BlogDetails` 任何類型的索引鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="12932-171">Notice that `BlogDetails` does not have any type of key property.</span></span> <span data-ttu-id="12932-172">在網域導向設計中， `BlogDetails` 稱為值物件。</span><span class="sxs-lookup"><span data-stu-id="12932-172">In domain driven design, `BlogDetails` is referred to as a value object.</span></span> <span data-ttu-id="12932-173">Entity Framework 將值物件參考為複雜類型。</span><span class="sxs-lookup"><span data-stu-id="12932-173">Entity Framework refers to value objects as complex types.</span></span><span data-ttu-id="12932-174">複雜類型無法自行追蹤。</span><span class="sxs-lookup"><span data-stu-id="12932-174">  Complex types cannot be tracked on their own.</span></span>

<span data-ttu-id="12932-175">但是，做為類別中的屬性時 `Blog` ， `BlogDetails` 將會追蹤為物件的一部分 `Blog` 。</span><span class="sxs-lookup"><span data-stu-id="12932-175">However as a property in the `Blog` class, `BlogDetails` will be tracked as part of a `Blog` object.</span></span> <span data-ttu-id="12932-176">為了讓 code first 辨識這一點，您必須將類別標記 `BlogDetails` 為 `ComplexType` 。</span><span class="sxs-lookup"><span data-stu-id="12932-176">In order for code first to recognize this, you must mark the `BlogDetails` class as a `ComplexType`.</span></span>

``` csharp
    [ComplexType]
    public class BlogDetails
    {
        public DateTime? DateCreated { get; set; }

        [MaxLength(250)]
        public string Description { get; set; }
    }
```

<span data-ttu-id="12932-177">現在您可以在類別中加入屬性， `Blog` 以代表 `BlogDetails` 該 blog 的。</span><span class="sxs-lookup"><span data-stu-id="12932-177">Now you can add a property in the `Blog` class to represent the `BlogDetails` for that blog.</span></span>

``` csharp
        public BlogDetails BlogDetail { get; set; }
```

<span data-ttu-id="12932-178">在資料庫中， `Blog` 資料表將會包含所有 blog 的屬性，包括其屬性中包含的屬性 `BlogDetail` 。</span><span class="sxs-lookup"><span data-stu-id="12932-178">In the database, the `Blog` table will contain all of the properties of the blog including the properties contained in its `BlogDetail` property.</span></span> <span data-ttu-id="12932-179">根據預設，每個名稱前面都會加上複雜型別 "BlogDetail" 的名稱。</span><span class="sxs-lookup"><span data-stu-id="12932-179">By default, each one is preceded with the name of the complex type, "BlogDetail".</span></span>

![具有複雜類型的 Blog 資料表](~/ef6/media/jj591583-figure06.png)


## <a name="concurrencycheck"></a><span data-ttu-id="12932-181">ConcurrencyCheck</span><span class="sxs-lookup"><span data-stu-id="12932-181">ConcurrencyCheck</span></span>

<span data-ttu-id="12932-182">`ConcurrencyCheck`當使用者編輯或刪除實體時，批註可讓您將一或多個屬性標示為要用於資料庫中的平行存取檢查。</span><span class="sxs-lookup"><span data-stu-id="12932-182">The `ConcurrencyCheck` annotation allows you to flag one or more properties to be used for concurrency checking in the database when a user edits or deletes an entity.</span></span> <span data-ttu-id="12932-183">如果您使用的是 EF 設計工具，則會將屬性的設定 `ConcurrencyMode` 為 `Fixed` 。</span><span class="sxs-lookup"><span data-stu-id="12932-183">If you've been working with the EF Designer, this aligns with setting a property's `ConcurrencyMode` to `Fixed`.</span></span>

<span data-ttu-id="12932-184">讓我們來看看如何藉 `ConcurrencyCheck` 由將它新增至屬性來運作 `BloggerName` 。</span><span class="sxs-lookup"><span data-stu-id="12932-184">Let’s see how `ConcurrencyCheck` works by adding it to the `BloggerName` property.</span></span>

``` csharp
    [ConcurrencyCheck, MaxLength(10, ErrorMessage="BloggerName must be 10 characters or less"),MinLength(5)]
    public string BloggerName { get; set; }
```

<span data-ttu-id="12932-185">當 `SaveChanges` 呼叫時，因為 `ConcurrencyCheck` 欄位上的注釋 `BloggerName` ，所以會在更新中使用該屬性的原始值。</span><span class="sxs-lookup"><span data-stu-id="12932-185">When `SaveChanges` is called, because of the `ConcurrencyCheck` annotation on the `BloggerName` field, the original value of that property will be used in the update.</span></span> <span data-ttu-id="12932-186">此命令會藉由篩選不只是在索引鍵值，而是在的原始值上，來嘗試找出正確的資料列 `BloggerName` 。</span><span class="sxs-lookup"><span data-stu-id="12932-186">The command will attempt to locate the correct row by filtering not only on the key value but also on the original value of `BloggerName`.</span></span><span data-ttu-id="12932-187">以下是傳送至資料庫之 UPDATE 命令的重要部分，您可以在其中看到此命令會更新具有1的資料列 `PrimaryTrackingKey` 和 "Julie" 的資料列， `BloggerName` 這是從資料庫取出該 blog 的原始值。</span><span class="sxs-lookup"><span data-stu-id="12932-187">  Here are the critical parts of the UPDATE command sent to the database, where you can see the command will update the row that has a `PrimaryTrackingKey` is 1 and a `BloggerName` of “Julie” which was the original value when that blog was retrieved from the database.</span></span>

``` SQL
    where (([PrimaryTrackingKey] = @4) and ([BloggerName] = @5))
    @4=1,@5=N'Julie'
```

<span data-ttu-id="12932-188">如果有人在此同時變更了該 blog 的 blog 名稱，此更新將會失敗，而且您將會收到需要處理的 **DbUpdateConcurrencyException** 。</span><span class="sxs-lookup"><span data-stu-id="12932-188">If someone has changed the blogger name for that blog in the meantime, this update will fail and you’ll get a **DbUpdateConcurrencyException** that you'll need to handle.</span></span>

 

## <a name="timestamp"></a><span data-ttu-id="12932-189">TimeStamp</span><span class="sxs-lookup"><span data-stu-id="12932-189">TimeStamp</span></span>

<span data-ttu-id="12932-190">針對平行存取檢查使用 rowversion 或時間戳記欄位更為普遍。</span><span class="sxs-lookup"><span data-stu-id="12932-190">It's more common to use rowversion or timestamp fields for concurrency checking.</span></span> <span data-ttu-id="12932-191">但是 `ConcurrencyCheck` ， `TimeStamp` 只要屬性的類型是位元組陣列，您就可以使用較特定的注釋，而不是使用批註。</span><span class="sxs-lookup"><span data-stu-id="12932-191">But rather than using the `ConcurrencyCheck` annotation, you can use the more specific `TimeStamp` annotation as long as the type of the property is byte array.</span></span> <span data-ttu-id="12932-192">Code first 會將 `Timestamp` 屬性視為與 `ConcurrencyCheck` 屬性相同，但它也會確保程式碼第一個產生的資料庫欄位不是可為 null。</span><span class="sxs-lookup"><span data-stu-id="12932-192">Code first will treat `Timestamp` properties the same as `ConcurrencyCheck` properties, but it will also ensure that the database field that code first generates is non-nullable.</span></span> <span data-ttu-id="12932-193">在指定的類別中，您只能有一個時間戳記屬性。</span><span class="sxs-lookup"><span data-stu-id="12932-193">You can only have one timestamp property in a given class.</span></span>

<span data-ttu-id="12932-194">將下列屬性新增至 Blog 類別：</span><span class="sxs-lookup"><span data-stu-id="12932-194">Adding the following property to the Blog class:</span></span>

``` csharp
    [Timestamp]
    public Byte[] TimeStamp { get; set; }
```

<span data-ttu-id="12932-195">導致程式碼先在資料庫資料表中建立不可為 null 的時間戳記資料行。</span><span class="sxs-lookup"><span data-stu-id="12932-195">results in code first creating a non-nullable timestamp column in the database table.</span></span>

![具有時間戳記資料行的 blog 資料表](~/ef6/media/jj591583-figure07.png)

 

## <a name="table-and-column"></a><span data-ttu-id="12932-197">資料表和資料行</span><span class="sxs-lookup"><span data-stu-id="12932-197">Table and Column</span></span>

<span data-ttu-id="12932-198">如果您讓 Code First 建立資料庫，您可能會想要變更其所建立之資料表和資料行的名稱。</span><span class="sxs-lookup"><span data-stu-id="12932-198">If you are letting Code First create the database, you may want to change the name of the tables and columns it is creating.</span></span> <span data-ttu-id="12932-199">您也可以搭配現有的資料庫使用 Code First。</span><span class="sxs-lookup"><span data-stu-id="12932-199">You can also use Code First with an existing database.</span></span> <span data-ttu-id="12932-200">但是，您的網域中的類別和屬性名稱不一定會符合資料庫中的資料表和資料行名稱。</span><span class="sxs-lookup"><span data-stu-id="12932-200">But it's not always the case that the names of the classes and properties in your domain match the names of the tables and columns in your database.</span></span>

<span data-ttu-id="12932-201">我的類別命名為 `Blog` and by 慣例，code first 會假設這會對應到名為的資料表 `Blogs` 。</span><span class="sxs-lookup"><span data-stu-id="12932-201">My class is named `Blog` and by convention, code first presumes this will map to a table named `Blogs`.</span></span> <span data-ttu-id="12932-202">如果不是這種情況，您可以使用屬性來指定資料表的名稱 `Table` 。</span><span class="sxs-lookup"><span data-stu-id="12932-202">If that's not the case you can specify the name of the table with the `Table` attribute.</span></span> <span data-ttu-id="12932-203">例如，批註會指定資料表名稱是 **InternalBlogs**。</span><span class="sxs-lookup"><span data-stu-id="12932-203">Here for example, the annotation is specifying that the table name is **InternalBlogs**.</span></span>

``` csharp
    [Table("InternalBlogs")]
    public class Blog
```

<span data-ttu-id="12932-204">`Column`批註更能指定對應資料行的屬性。</span><span class="sxs-lookup"><span data-stu-id="12932-204">The `Column` annotation is a more adept in specifying the attributes of a mapped column.</span></span> <span data-ttu-id="12932-205">您可以規定名稱、資料類型，甚至是資料行出現在資料表中的順序。</span><span class="sxs-lookup"><span data-stu-id="12932-205">You can stipulate a name, data type or even the order in which a column appears in the table.</span></span> <span data-ttu-id="12932-206">以下是屬性的範例 `Column` 。</span><span class="sxs-lookup"><span data-stu-id="12932-206">Here is an example of the `Column` attribute.</span></span>

``` csharp
    [Column("BlogDescription", TypeName="ntext")]
    public String Description {get;set;}
```

<span data-ttu-id="12932-207">請勿 `TypeName` 將資料行的屬性與 DataType DataAnnotation 混淆。</span><span class="sxs-lookup"><span data-stu-id="12932-207">Don’t confuse Column’s `TypeName` attribute with the DataType DataAnnotation.</span></span> <span data-ttu-id="12932-208">DataType 是用於 UI 的注釋，Code First 會忽略此批註。</span><span class="sxs-lookup"><span data-stu-id="12932-208">DataType is an annotation used for the UI and is ignored by Code First.</span></span>

<span data-ttu-id="12932-209">以下是重新產生資料表之後的資料表。</span><span class="sxs-lookup"><span data-stu-id="12932-209">Here is the table after it’s been regenerated.</span></span> <span data-ttu-id="12932-210">資料表名稱已變更為 **InternalBlogs** ，而 `Description` 複雜類型中的資料行現在已變更 `BlogDescription` 。</span><span class="sxs-lookup"><span data-stu-id="12932-210">The table name has changed to **InternalBlogs** and `Description` column from the complex type is now `BlogDescription`.</span></span> <span data-ttu-id="12932-211">因為名稱是在批註中指定，所以 code first 不會使用以複雜型別名稱來啟動資料行名稱的慣例。</span><span class="sxs-lookup"><span data-stu-id="12932-211">Because the name was specified in the annotation, code first will not use the convention of starting the column name with the name of the complex type.</span></span>

![重新命名的 blog 資料表和資料行](~/ef6/media/jj591583-figure08.png)

 

## <a name="databasegenerated"></a><span data-ttu-id="12932-213">DatabaseGenerated</span><span class="sxs-lookup"><span data-stu-id="12932-213">DatabaseGenerated</span></span>

<span data-ttu-id="12932-214">重要的資料庫功能是具有計算屬性的能力。</span><span class="sxs-lookup"><span data-stu-id="12932-214">An important database features is the ability to have computed properties.</span></span> <span data-ttu-id="12932-215">如果您要將 Code First 類別對應至包含計算資料行的資料表，則不希望 Entity Framework 嘗試更新這些資料行。</span><span class="sxs-lookup"><span data-stu-id="12932-215">If you're mapping your Code First classes to tables that contain computed columns, you don't want Entity Framework to try to update those columns.</span></span> <span data-ttu-id="12932-216">但是，您希望 EF 在插入或更新資料之後，從資料庫傳回這些值。</span><span class="sxs-lookup"><span data-stu-id="12932-216">But you do want EF to return those values from the database after you've inserted or updated data.</span></span> <span data-ttu-id="12932-217">您可以使用 `DatabaseGenerated` 批註來標記類別中的那些屬性以及 `Computed` 列舉。</span><span class="sxs-lookup"><span data-stu-id="12932-217">You can use the `DatabaseGenerated` annotation to flag those properties in your class along with the `Computed` enum.</span></span> <span data-ttu-id="12932-218">其他列舉為 `None` 和 `Identity` 。</span><span class="sxs-lookup"><span data-stu-id="12932-218">Other enums are `None` and `Identity`.</span></span>

``` csharp
    [DatabaseGenerated(DatabaseGeneratedOption.Computed)]
    public DateTime DateCreated { get; set; }
```

<span data-ttu-id="12932-219">當 code first 產生資料庫時，您可以使用在位元組或時間戳記資料行上產生的資料庫，否則您應該只在指向現有的資料庫時使用此資料庫，因為 code first 將無法判斷計算資料行的公式。</span><span class="sxs-lookup"><span data-stu-id="12932-219">You can use database generated on byte or timestamp columns when code first is generating the database, otherwise you should only use this when pointing to existing databases because code first won't be able to determine the formula for the computed column.</span></span>

<span data-ttu-id="12932-220">您會在上面閱讀過，預設為整數的索引鍵屬性會成為資料庫中的識別索引鍵。</span><span class="sxs-lookup"><span data-stu-id="12932-220">You read above that by default, a key property that is an integer will become an identity key in the database.</span></span> <span data-ttu-id="12932-221">這與設定相同 `DatabaseGenerated` `DatabaseGeneratedOption.Identity` 。</span><span class="sxs-lookup"><span data-stu-id="12932-221">That would be the same as setting `DatabaseGenerated` to `DatabaseGeneratedOption.Identity`.</span></span> <span data-ttu-id="12932-222">如果您不想讓它成為身分識別索引鍵，可以將值設定為 `DatabaseGeneratedOption.None` 。</span><span class="sxs-lookup"><span data-stu-id="12932-222">If you do not want it to be an identity key, you can set the value to `DatabaseGeneratedOption.None`.</span></span>

 

## <a name="index"></a><span data-ttu-id="12932-223">索引</span><span class="sxs-lookup"><span data-stu-id="12932-223">Index</span></span>

> [!NOTE]
> <span data-ttu-id="12932-224">**僅限 ef 6.1** -此 `Index` 屬性是在 Entity Framework 6.1 中引進。</span><span class="sxs-lookup"><span data-stu-id="12932-224">**EF6.1 Onwards Only** - The `Index` attribute was introduced in Entity Framework 6.1.</span></span> <span data-ttu-id="12932-225">如果您使用的是較舊的版本，本節中的資訊並不適用。</span><span class="sxs-lookup"><span data-stu-id="12932-225">If you are using an earlier version the information in this section does not apply.</span></span>

<span data-ttu-id="12932-226">您可以使用 **IndexAttribute**，在一個或多個資料行上建立索引。</span><span class="sxs-lookup"><span data-stu-id="12932-226">You can create an index on one or more columns using the **IndexAttribute**.</span></span> <span data-ttu-id="12932-227">將屬性加入至一個或多個屬性，將會導致 EF 在建立資料庫時于資料庫中建立對應的索引，如果您使用 Code First 移轉，則會 scaffold 對應的 **CreateIndex** 呼叫。</span><span class="sxs-lookup"><span data-stu-id="12932-227">Adding the attribute to one or more properties will cause EF to create the corresponding index in the database when it creates the database, or scaffold the corresponding **CreateIndex** calls if you are using Code First Migrations.</span></span>

<span data-ttu-id="12932-228">例如，下列程式碼會在 `Rating` 資料庫的資料表資料行上建立索引 `Posts` 。</span><span class="sxs-lookup"><span data-stu-id="12932-228">For example, the following code will result in an index being created on the `Rating` column of the `Posts` table in the database.</span></span>

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

<span data-ttu-id="12932-229">根據預設，在上述範例中，索引將會命名為**ix \_ &lt; &gt; 屬性名稱** (ix \_ 分級) 。</span><span class="sxs-lookup"><span data-stu-id="12932-229">By default, the index will be named **IX\_&lt;property name&gt;** (IX\_Rating in the above example).</span></span> <span data-ttu-id="12932-230">不過，您也可以指定索引的名稱。</span><span class="sxs-lookup"><span data-stu-id="12932-230">You can also specify a name for the index though.</span></span> <span data-ttu-id="12932-231">下列範例會指定索引應該命名為 `PostRatingIndex` 。</span><span class="sxs-lookup"><span data-stu-id="12932-231">The following example specifies that the index should be named `PostRatingIndex`.</span></span>

``` csharp
    [Index("PostRatingIndex")]
    public int Rating { get; set; }
```

<span data-ttu-id="12932-232">根據預設，索引不是唯一的，但您可以使用 `IsUnique` 具名引數來指定索引應該是唯一的。</span><span class="sxs-lookup"><span data-stu-id="12932-232">By default, indexes are non-unique, but you can use the `IsUnique` named parameter to specify that an index should be unique.</span></span> <span data-ttu-id="12932-233">下列範例會在的登入名稱上引進唯一索引 `User` 。</span><span class="sxs-lookup"><span data-stu-id="12932-233">The following example introduces a unique index on a `User`'s login name.</span></span>

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

### <a name="multiple-column-indexes"></a><span data-ttu-id="12932-234">多重資料行索引</span><span class="sxs-lookup"><span data-stu-id="12932-234">Multiple-Column Indexes</span></span>

<span data-ttu-id="12932-235">跨多個資料行的索引是使用相同的名稱，在指定資料表的多個索引注釋中使用相同的名稱所指定。</span><span class="sxs-lookup"><span data-stu-id="12932-235">Indexes that span multiple columns are specified by using the same name in multiple Index annotations for a given table.</span></span> <span data-ttu-id="12932-236">當您建立多個資料行的索引時，您需要指定索引中資料行的順序。</span><span class="sxs-lookup"><span data-stu-id="12932-236">When you create multi-column indexes, you need to specify an order for the columns in the index.</span></span> <span data-ttu-id="12932-237">例如，下列程式碼會在上建立多資料行索引 `Rating` ，並 `BlogId` 呼叫 **IX \_ BlogIdAndRating**。</span><span class="sxs-lookup"><span data-stu-id="12932-237">For example, the following code creates a multi-column index on `Rating` and `BlogId` called **IX\_BlogIdAndRating**.</span></span> <span data-ttu-id="12932-238">`BlogId` 這是索引中的第一個資料行，而 `Rating` 是第二個數據行。</span><span class="sxs-lookup"><span data-stu-id="12932-238">`BlogId` is the first column in the index and `Rating` is the second.</span></span>

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

 

## <a name="relationship-attributes-inverseproperty-and-foreignkey"></a><span data-ttu-id="12932-239">關聯性屬性： InverseProperty 和 ForeignKey</span><span class="sxs-lookup"><span data-stu-id="12932-239">Relationship Attributes: InverseProperty and ForeignKey</span></span>

> [!NOTE]
> <span data-ttu-id="12932-240">此頁面提供使用資料批註在 Code First 模型中設定關聯性的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="12932-240">This page provides information about setting up relationships in your Code First model using Data Annotations.</span></span> <span data-ttu-id="12932-241">如需 EF 中關聯性的一般資訊，以及如何使用關聯性存取和運算元據，請參閱 [關聯性 & 導覽屬性](~/ef6/fundamentals/relationships.md)。 \*</span><span class="sxs-lookup"><span data-stu-id="12932-241">For general information about relationships in EF and how to access and manipulate data using relationships, see [Relationships & Navigation Properties](~/ef6/fundamentals/relationships.md).\*</span></span>

<span data-ttu-id="12932-242">Code first 慣例將負責您模型中最常見的關聯性，但在某些情況下需要協助。</span><span class="sxs-lookup"><span data-stu-id="12932-242">Code first convention will take care of the most common relationships in your model, but there are some cases where it needs help.</span></span>

<span data-ttu-id="12932-243">變更類別中的索引鍵屬性名稱時，會 `Blog` 產生與其關聯性的問題 `Post` 。</span><span class="sxs-lookup"><span data-stu-id="12932-243">Changing the name of the key property in the `Blog` class created a problem with its relationship to `Post`.</span></span> 

<span data-ttu-id="12932-244">產生資料庫時，code first 會看到 `BlogId` Post 類別中的屬性，並根據其符合類別名稱加上 **識別碼**的慣例加以辨識，以做為類別的外鍵 `Blog` 。</span><span class="sxs-lookup"><span data-stu-id="12932-244">When generating the database, code first sees the `BlogId` property in the Post class and recognizes it, by the convention that it matches a class name plus **Id**, as a foreign key to the `Blog` class.</span></span> <span data-ttu-id="12932-245">但在 `BlogId` blog 類別中沒有屬性。</span><span class="sxs-lookup"><span data-stu-id="12932-245">But there is no `BlogId` property in the blog class.</span></span> <span data-ttu-id="12932-246">的解決方法是在中建立導覽屬性 `Post` ，並使用 `ForeignKey` DataAnnotation 來協助程式碼先瞭解如何建立兩個類別之間的關聯性， (使用 `Post.BlogId` 屬性) 以及如何在資料庫中指定條件約束。</span><span class="sxs-lookup"><span data-stu-id="12932-246">The solution for this is to create a navigation property in the `Post` and use the `ForeignKey` DataAnnotation to help code first understand how to build the relationship between the two classes (using the `Post.BlogId` property) as well as how to specify constraints in the database.</span></span>

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

<span data-ttu-id="12932-247">資料庫中的條件約束會顯示與之間的關聯性 `InternalBlogs.PrimaryTrackingKey` `Posts.BlogId` 。</span><span class="sxs-lookup"><span data-stu-id="12932-247">The constraint in the database shows a relationship between `InternalBlogs.PrimaryTrackingKey` and `Posts.BlogId`.</span></span> 

![InternalBlogs. PrimaryTrackingKey 與 BlogId 之間的關聯性](~/ef6/media/jj591583-figure09.png)

<span data-ttu-id="12932-249">`InverseProperty`當您在類別之間有多個關聯性時，會使用。</span><span class="sxs-lookup"><span data-stu-id="12932-249">The `InverseProperty` is used when you have multiple relationships between classes.</span></span>

<span data-ttu-id="12932-250">在 `Post` 類別中，您可能會想要追蹤誰寫了 blog 貼文以及編輯者。</span><span class="sxs-lookup"><span data-stu-id="12932-250">In the `Post` class, you may want to keep track of who wrote a blog post as well as who edited it.</span></span> <span data-ttu-id="12932-251">以下是 Post 類別的兩個新導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="12932-251">Here are two new navigation properties for the Post class.</span></span>

``` csharp
    public Person CreatedBy { get; set; }
    public Person UpdatedBy { get; set; }
```

<span data-ttu-id="12932-252">您也需要在 `Person` 這些屬性所參考的類別中加入。</span><span class="sxs-lookup"><span data-stu-id="12932-252">You’ll also need to add in the `Person` class referenced by these properties.</span></span> <span data-ttu-id="12932-253">此 `Person` 類別具有導覽屬性，而該屬性是 `Post` 由人員撰寫的所有文章所撰寫，另一個則是該人員所更新的所有文章。</span><span class="sxs-lookup"><span data-stu-id="12932-253">The `Person` class has navigation properties back to the `Post`, one for all of the posts written by the person and one for all of the posts updated by that person.</span></span>

``` csharp
    public class Person
    {
            public int Id { get; set; }
            public string Name { get; set; }
            public List<Post> PostsWritten { get; set; }
            public List<Post> PostsUpdated { get; set; }
    }
```

<span data-ttu-id="12932-254">Code first 無法自行比對兩個類別中的屬性。</span><span class="sxs-lookup"><span data-stu-id="12932-254">Code first is not able to match up the properties in the two classes on its own.</span></span> <span data-ttu-id="12932-255">的資料庫資料表 `Posts` 應該要有一個外鍵 `CreatedBy` ，另一個用於 `UpdatedBy` 人員，但 code first 會建立四個外鍵屬性： **person \_ Id**、 **person \_ Id1**、 **CreatedBy \_ Id** 和 **UpdatedBy \_ Id**。</span><span class="sxs-lookup"><span data-stu-id="12932-255">The database table for `Posts` should have one foreign key for the `CreatedBy` person and one for the `UpdatedBy` person but code first will create four foreign key properties: **Person\_Id**, **Person\_Id1**, **CreatedBy\_Id** and **UpdatedBy\_Id**.</span></span>

![附有額外外鍵的貼文資料表](~/ef6/media/jj591583-figure10.png)

<span data-ttu-id="12932-257">若要修正這些問題，您可以使用 `InverseProperty` 批註來指定屬性的對齊方式。</span><span class="sxs-lookup"><span data-stu-id="12932-257">To fix these problems, you can use the `InverseProperty` annotation to specify the alignment of the properties.</span></span>

``` csharp
    [InverseProperty("CreatedBy")]
    public List<Post> PostsWritten { get; set; }

    [InverseProperty("UpdatedBy")]
    public List<Post> PostsUpdated { get; set; }
```

<span data-ttu-id="12932-258">因為 `PostsWritten` Person 中的屬性知道這會參考型別 `Post` ，所以它會建立與的關聯性 `Post.CreatedBy` 。</span><span class="sxs-lookup"><span data-stu-id="12932-258">Because the `PostsWritten` property in Person knows that this refers to the `Post` type, it will build the relationship to `Post.CreatedBy`.</span></span> <span data-ttu-id="12932-259">同樣地， `PostsUpdated` 會連接到 `Post.UpdatedBy` 。</span><span class="sxs-lookup"><span data-stu-id="12932-259">Similarly, `PostsUpdated` will be connected to `Post.UpdatedBy`.</span></span> <span data-ttu-id="12932-260">而且 code first 將不會建立額外的外鍵。</span><span class="sxs-lookup"><span data-stu-id="12932-260">And code first will not create the extra foreign keys.</span></span>

![Post 資料表沒有額外的外鍵](~/ef6/media/jj591583-figure11.png)

 

## <a name="summary"></a><span data-ttu-id="12932-262">摘要</span><span class="sxs-lookup"><span data-stu-id="12932-262">Summary</span></span>

<span data-ttu-id="12932-263">DataAnnotations 不僅可讓您描述程式碼優先類別中的用戶端和伺服器端驗證，還可讓您根據程式碼的慣例，加強或修正 code first 針對您的類別所做的假設。</span><span class="sxs-lookup"><span data-stu-id="12932-263">DataAnnotations not only let you describe client and server side validation in your code first classes, but they also allow you to enhance and even correct the assumptions that code first will make about your classes based on its conventions.</span></span> <span data-ttu-id="12932-264">使用 DataAnnotations 時，您不僅可以驅動資料庫架構的產生，還可以將您的 code first 類別對應到既有的資料庫。</span><span class="sxs-lookup"><span data-stu-id="12932-264">With DataAnnotations you can not only drive database schema generation, but you can also map your code first classes to a pre-existing database.</span></span>

<span data-ttu-id="12932-265">雖然它們非常有彈性，但請記住，DataAnnotations 只會提供您在 code first 類別上最常需要的設定變更。</span><span class="sxs-lookup"><span data-stu-id="12932-265">While they are very flexible, keep in mind that DataAnnotations provide only the most commonly needed configuration changes you can make on your code first classes.</span></span> <span data-ttu-id="12932-266">若要針對某些邊緣案例設定您的類別，您應該尋找替代的設定機制，Code First 的流暢 API。</span><span class="sxs-lookup"><span data-stu-id="12932-266">To configure your classes for some of the edge cases, you should look to the alternate configuration mechanism, Code First’s Fluent API .</span></span>
