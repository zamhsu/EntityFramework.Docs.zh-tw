---
title: 驗證-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 77d6a095-c0d0-471e-80b9-8f9aea6108b2
ms.openlocfilehash: 457af0c32f0fe4804dbfe6e348664efb1af517c9
ms.sourcegitcommit: 7b7f774a5966b20d2aed5435a672a1edbe73b6fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/17/2019
ms.locfileid: "69565371"
---
# <a name="data-validation"></a><span data-ttu-id="d5bc9-102">資料驗證</span><span class="sxs-lookup"><span data-stu-id="d5bc9-102">Data Validation</span></span>
> [!NOTE]
> <span data-ttu-id="d5bc9-103">**僅限 ef 4.1 [僅限**]-此頁面中所討論的功能、api 等已在 Entity Framework 4.1 中引進。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-103">**EF4.1 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 4.1.</span></span> <span data-ttu-id="d5bc9-104">如果您使用的是較舊的版本, 部分或全部的資訊將不適用</span><span class="sxs-lookup"><span data-stu-id="d5bc9-104">If you are using an earlier version, some or all of the information does not apply</span></span>

<span data-ttu-id="d5bc9-105">此頁面上的內容可從原本由 Julie Lerman ([http://thedatafarm.com](http://thedatafarm.com)) 撰寫的文章進行調整。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-105">The content on this page is adapted from an article originally written by Julie Lerman ([http://thedatafarm.com](http://thedatafarm.com)).</span></span>

<span data-ttu-id="d5bc9-106">Entity Framework 提供了絕佳的驗證功能, 可將其饋送至使用者介面以進行用戶端驗證, 或用於伺服器端驗證。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-106">Entity Framework provides a great variety of validation features that can feed through to a user interface for client-side validation or be used for server-side validation.</span></span> <span data-ttu-id="d5bc9-107">使用 code first 時, 您可以使用 annotation 或 Fluent API 設定來指定驗證。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-107">When using code first, you can specify validations using annotation or fluent API configurations.</span></span> <span data-ttu-id="d5bc9-108">您可以在程式碼中指定額外的驗證和更複雜的, 而且不論您的模型是從 code first、model first 或 database first hails, 都能正常執行。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-108">Additional validations, and more complex, can be specified in code and will work whether your model hails from code first, model first or database first.</span></span>

## <a name="the-model"></a><span data-ttu-id="d5bc9-109">模型</span><span class="sxs-lookup"><span data-stu-id="d5bc9-109">The model</span></span>

<span data-ttu-id="d5bc9-110">我將使用一組簡單的類別來示範驗證:Blog 和 Post。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-110">I'll demonstrate the validations with a simple pair of classes: Blog and Post.</span></span>

``` csharp
public class Blog
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string BloggerName { get; set; }
    public DateTime DateCreated { get; set; }
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

## <a name="data-annotations"></a><span data-ttu-id="d5bc9-111">資料註釋</span><span class="sxs-lookup"><span data-stu-id="d5bc9-111">Data Annotations</span></span>

<span data-ttu-id="d5bc9-112">Code First 使用來自`System.ComponentModel.DataAnnotations`元件的注釋, 做為設定 code First 類別的其中一種方式。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-112">Code First uses annotations from the `System.ComponentModel.DataAnnotations` assembly as one means of configuring code first classes.</span></span> <span data-ttu-id="d5bc9-113">在這些批註中`Required`, 會提供、 `MaxLength`和`MinLength`等規則。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-113">Among these annotations are those which provide rules such as the `Required`, `MaxLength` and `MinLength`.</span></span> <span data-ttu-id="d5bc9-114">許多 .NET 用戶端應用程式也會辨識這些批註, 例如 ASP.NET MVC。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-114">A number of .NET client applications also recognize these annotations, for example, ASP.NET MVC.</span></span> <span data-ttu-id="d5bc9-115">您可以使用這些批註來完成用戶端和伺服器端驗證。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-115">You can achieve both client side and server side validation with these annotations.</span></span> <span data-ttu-id="d5bc9-116">例如, 您可以強制 [Blog 標題] 屬性成為必要的屬性。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-116">For example, you can force the Blog Title property to be a required property.</span></span>

``` csharp
[Required]
public string Title { get; set; }
```

<span data-ttu-id="d5bc9-117">在應用程式中不會有任何額外的程式碼或標記變更, 現有 MVC 應用程式會執行用戶端驗證, 甚至使用屬性和批註名稱動態建立訊息。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-117">With no additional code or markup changes in the application, an existing MVC application will perform client side validation, even dynamically building a message using the property and annotation names.</span></span>

![圖1](~/ef6/media/figure01.png)

<span data-ttu-id="d5bc9-119">在此 Create view 的回傳方法中, Entity Framework 是用來將新的 blog 儲存到資料庫中, 但是在應用程式達到該程式碼之前, 會觸發 MVC 的用戶端驗證。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-119">In the post back method of this Create view, Entity Framework is used to save the new blog to the database, but MVC's client-side validation is triggered before the application reaches that code.</span></span>

<span data-ttu-id="d5bc9-120">不過, 用戶端驗證並不是專案符號的證明。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-120">Client side validation is not bullet-proof however.</span></span> <span data-ttu-id="d5bc9-121">使用者可能會影響其瀏覽器的功能或更糟的部分, 駭客可能會使用某些 trickery 來避免 UI 驗證。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-121">Users can impact features of their browser or worse yet, a hacker might use some trickery to avoid the UI validations.</span></span> <span data-ttu-id="d5bc9-122">但是 Entity Framework 也會辨識`Required`注釋並加以驗證。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-122">But Entity Framework will also recognize the `Required` annotation and validate it.</span></span>

<span data-ttu-id="d5bc9-123">測試此程式的簡單方法是停用 MVC 的用戶端驗證功能。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-123">A simple way to test this is to disable MVC's client-side validation feature.</span></span> <span data-ttu-id="d5bc9-124">您可以在 MVC 應用程式的 web.config 檔案中執行此動作。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-124">You can do this in the MVC application's web.config file.</span></span> <span data-ttu-id="d5bc9-125">AppSettings 區段具有 ClientValidationEnabled 的索引鍵。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-125">The appSettings section has a key for ClientValidationEnabled.</span></span> <span data-ttu-id="d5bc9-126">將此機碼設為 false, 將會導致 UI 無法執行驗證。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-126">Setting this key to false will prevent the UI from performing validations.</span></span>

``` xml
<appSettings>
    <add key="ClientValidationEnabled"value="false"/>
    ...
</appSettings>
```

<span data-ttu-id="d5bc9-127">即使已停用用戶端驗證, 您仍會在應用程式中得到相同的回應。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-127">Even with the client-side validation disabled, you will get the same response in your application.</span></span> <span data-ttu-id="d5bc9-128">錯誤訊息「需要標題欄位」將會顯示為「必要」。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-128">The error message "The Title field is required" will be displayed as before.</span></span> <span data-ttu-id="d5bc9-129">但現在它會是伺服器端驗證的結果。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-129">Except now it will be a result of server-side validation.</span></span> <span data-ttu-id="d5bc9-130">Entity Framework 會在`Required`批註上執行驗證 (甚至是 bothers 來`INSERT`建立要傳送至資料庫的命令), 並將錯誤傳回至將顯示訊息的 MVC。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-130">Entity Framework will perform the validation on the `Required` annotation (before it even bothers to build an `INSERT` command to send to the database) and return the error to MVC which will display the message.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="d5bc9-131">Fluent API</span><span class="sxs-lookup"><span data-stu-id="d5bc9-131">Fluent API</span></span>

<span data-ttu-id="d5bc9-132">您可以使用 code first 的 Fluent API 來取代注釋, 以取得相同的用戶端 & 伺服器端驗證。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-132">You can use code first's fluent API instead of annotations to get the same client side & server side validation.</span></span> <span data-ttu-id="d5bc9-133">我會使用`Required`MaxLength 驗證來說明這一點, 而不是使用。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-133">Rather than use `Required`, I'll show you this using a MaxLength validation.</span></span>

<span data-ttu-id="d5bc9-134">在程式碼第一次從類別建立模型時, 會套用流暢的 API 設定。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-134">Fluent API configurations are applied as code first is building the model from the classes.</span></span> <span data-ttu-id="d5bc9-135">您可以藉由覆寫 DbCoNtext 類別的 OnModelCreating 方法來插入設定。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-135">You can inject the configurations by overriding the DbContext class' OnModelCreating  method.</span></span> <span data-ttu-id="d5bc9-136">以下是指定 BloggerName 屬性長度不能超過10個字元的設定。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-136">Here is a configuration specifying that the BloggerName property can be no longer than 10 characters.</span></span>

``` csharp
public class BlogContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
    public DbSet<Comment> Comments { get; set; }

    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>().Property(p => p.BloggerName).HasMaxLength(10);
    }
}
```

<span data-ttu-id="d5bc9-137">以流暢的 API 設定擲回的驗證錯誤不會自動到達 UI, 但是您可以在程式碼中加以捕捉, 然後據以回應。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-137">Validation errors thrown based on the Fluent API configurations will not automatically reach the UI, but you can capture it in code and then respond to it accordingly.</span></span>

<span data-ttu-id="d5bc9-138">以下是在應用程式的 BlogController 類別中, 當 Entity Framework 嘗試使用超過10個字元上限的 BloggerName 來儲存 blog 時, 會發生該驗證錯誤的部分例外狀況處理錯誤碼。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-138">Here's some exception handling error code in the application's BlogController class that captures that validation error when Entity Framework attempts to save a blog with a BloggerName that exceeds the 10 character maximum.</span></span>

``` csharp
[HttpPost]
public ActionResult Edit(int id, Blog blog)
{
    try
    {
        db.Entry(blog).State = EntityState.Modified;
        db.SaveChanges();
        return RedirectToAction("Index");
    }
    catch (DbEntityValidationException ex)
    {
        var error = ex.EntityValidationErrors.First().ValidationErrors.First();
        this.ModelState.AddModelError(error.PropertyName, error.ErrorMessage);
        return View();
    }
}
```

<span data-ttu-id="d5bc9-139">驗證不會自動傳回給視圖, 這就是使用使用`ModelState.AddModelError`的其他程式碼的原因。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-139">The validation doesn't automatically get passed back into the view which is why the additional code that uses `ModelState.AddModelError` is being used.</span></span> <span data-ttu-id="d5bc9-140">這可確保錯誤詳細資料會使其成為視圖, 然後使用`ValidationMessageFor` Htmlhelper 來顯示錯誤。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-140">This ensures that the error details make it to the view which will then use the `ValidationMessageFor` Htmlhelper to display the error.</span></span>

``` csharp
@Html.ValidationMessageFor(model => model.BloggerName)
```

## <a name="ivalidatableobject"></a><span data-ttu-id="d5bc9-141">IValidatableObject</span><span class="sxs-lookup"><span data-stu-id="d5bc9-141">IValidatableObject</span></span>

<span data-ttu-id="d5bc9-142">`IValidatableObject`是存在的介面`System.ComponentModel.DataAnnotations`。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-142">`IValidatableObject` is an interface that lives in `System.ComponentModel.DataAnnotations`.</span></span> <span data-ttu-id="d5bc9-143">雖然它不是 Entity Framework API 的一部分, 但您仍然可以將它運用在 Entity Framework 類別中的伺服器端驗證。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-143">While it is not part of the Entity Framework API, you can still leverage it for server-side validation in your Entity Framework classes.</span></span> <span data-ttu-id="d5bc9-144">`IValidatableObject`提供一`Validate`種方法, Entity Framework 會在 SaveChanges 期間呼叫, 或您可以在任何時候想要驗證類別時呼叫。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-144">`IValidatableObject` provides a `Validate` method that Entity Framework will call during SaveChanges or you can call yourself any time you want to validate the classes.</span></span>

<span data-ttu-id="d5bc9-145">`Required` 之類`MaxLength`的設定會在單一欄位上執行驗證。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-145">Configurations such as `Required` and `MaxLength` perform validation on a single field.</span></span> <span data-ttu-id="d5bc9-146">`Validate`在方法中, 您甚至可以擁有更複雜的邏輯, 例如比較兩個欄位。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-146">In the `Validate` method you can have even more complex logic, for example, comparing two fields.</span></span>

<span data-ttu-id="d5bc9-147">在`Blog`下列範例中, 類別已擴充為可執行`IValidatableObject` , 然後`Title`提供符合和`BloggerName`無法比對的規則。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-147">In the following example, the `Blog` class has been extended to implement `IValidatableObject` and then provide a rule that the `Title` and `BloggerName` cannot match.</span></span>

``` csharp
public class Blog : IValidatableObject
{
    public int Id { get; set; }

    [Required]
    public string Title { get; set; }

    public string BloggerName { get; set; }
    public DateTime DateCreated { get; set; }
    public virtual ICollection<Post> Posts { get; set; }

    public IEnumerable<ValidationResult> Validate(ValidationContext validationContext)
    {
        if (Title == BloggerName)
        {
            yield return new ValidationResult(
                "Blog Title cannot match Blogger Name",
                new[] { nameof(Title), nameof(BloggerName) });
        }
    }
}
```

<span data-ttu-id="d5bc9-148">此`ValidationResult`函式`string`會採用代表錯誤`string`訊息的, 以及代表與驗證相關聯之成員名稱的陣列。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-148">The `ValidationResult` constructor takes a `string` that represents the error message and an array of `string`s that represent the member names that are associated with the validation.</span></span> <span data-ttu-id="d5bc9-149">因為這項驗證會同時`Title`檢查`BloggerName`和, 所以會傳回這兩個屬性名稱。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-149">Since this validation checks both the `Title` and the `BloggerName`, both property names are returned.</span></span>

<span data-ttu-id="d5bc9-150">不同于流暢 API 所提供的驗證, 此驗證結果會由視圖辨識, 而我先前用來將錯誤新增至`ModelState`的例外狀況處理常式是不必要的。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-150">Unlike the validation provided by the Fluent API, this validation result will be recognized by the View and the exception handler that I used earlier to add the error into `ModelState` is unnecessary.</span></span> <span data-ttu-id="d5bc9-151">因為我在中設定了`ValidationResult`這兩個屬性名稱, 所以 MVC HtmlHelpers 會顯示這兩個屬性的錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-151">Because I set both property names in the `ValidationResult`, the MVC HtmlHelpers display the error message for both of those properties.</span></span>

![圖2](~/ef6/media/figure02.png)

## <a name="dbcontextvalidateentity"></a><span data-ttu-id="d5bc9-153">DbContext.ValidateEntity</span><span class="sxs-lookup"><span data-stu-id="d5bc9-153">DbContext.ValidateEntity</span></span>

<span data-ttu-id="d5bc9-154">`DbContext`具有名`ValidateEntity`為的可覆寫方法。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-154">`DbContext` has an overridable method called `ValidateEntity`.</span></span> <span data-ttu-id="d5bc9-155">當您呼叫`SaveChanges`時, Entity Framework 會針對其狀態不`Unchanged`是的快取中的每個實體呼叫此方法。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-155">When you call `SaveChanges`, Entity Framework will call this method for each entity in its cache whose state is not `Unchanged`.</span></span> <span data-ttu-id="d5bc9-156">您可以將驗證邏輯直接放在這裡, 或甚至使用這個方法來呼叫, 例如上`Blog.Validate`一節中加入的方法。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-156">You can put validation logic directly in here or even use this method to call, for example, the `Blog.Validate` method added in the previous section.</span></span>

<span data-ttu-id="d5bc9-157">以下是一個`ValidateEntity`覆寫的範例, 它會`Post`驗證新的, 以確保貼文標題尚未使用。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-157">Here's an example of a `ValidateEntity` override that validates new `Post`s to ensure that the post title hasn't been used already.</span></span> <span data-ttu-id="d5bc9-158">它會先檢查實體是否為 post, 並加入其狀態。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-158">It first checks to see if the entity is a post and that its state is Added.</span></span> <span data-ttu-id="d5bc9-159">如果是這種情況, 則會在資料庫中查看是否已有相同標題的貼文。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-159">If that's the case, then it looks in the database to see if there is already a post with the same title.</span></span> <span data-ttu-id="d5bc9-160">如果已經有現有的貼文, 則會建立`DbEntityValidationResult`新的。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-160">If there is an existing post already, then a new `DbEntityValidationResult` is created.</span></span>

<span data-ttu-id="d5bc9-161">`DbEntityValidationResult``ICollection<DbValidationErrors>` `DbEntityEntry`為單一實體裝載和。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-161">`DbEntityValidationResult` houses a `DbEntityEntry` and an `ICollection<DbValidationErrors>` for a single entity.</span></span> <span data-ttu-id="d5bc9-162">在這個方法的開頭, `DbEntityValidationResult`會具現化, 然後將探索到的任何錯誤新增至其`ValidationErrors`集合。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-162">At the start of this method, a `DbEntityValidationResult` is instantiated and then any errors that are discovered are added into its `ValidationErrors` collection.</span></span>

``` csharp
protected override DbEntityValidationResult ValidateEntity (
    System.Data.Entity.Infrastructure.DbEntityEntry entityEntry,
    IDictionary<object, object> items)
{
    var result = new DbEntityValidationResult(entityEntry, new List<DbValidationError>());

    if (entityEntry.Entity is Post post && entityEntry.State == EntityState.Added)
    {
        // Check for uniqueness of post title
        if (Posts.Where(p => p.Title == post.Title).Any())
        {
            result.ValidationErrors.Add(
                    new System.Data.Entity.Validation.DbValidationError(
                        nameof(Title),
                        "Post title must be unique."));
        }
    }

    if (result.ValidationErrors.Count > 0)
    {
        return result;
    }
    else
    {
        return base.ValidateEntity(entityEntry, items);
    }
}
```

## <a name="explicitly-triggering-validation"></a><span data-ttu-id="d5bc9-163">明確觸發驗證</span><span class="sxs-lookup"><span data-stu-id="d5bc9-163">Explicitly triggering validation</span></span>

<span data-ttu-id="d5bc9-164">呼叫`SaveChanges`會觸發本文中涵蓋的所有驗證。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-164">A call to `SaveChanges` triggers all of the validations covered in this article.</span></span> <span data-ttu-id="d5bc9-165">但是您不需要依賴`SaveChanges`。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-165">But you don't need to rely on `SaveChanges`.</span></span> <span data-ttu-id="d5bc9-166">您可能想要在應用程式的其他地方進行驗證。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-166">You may prefer to validate elsewhere in your application.</span></span>

<span data-ttu-id="d5bc9-167">`DbContext.GetValidationErrors`會觸發所有驗證, 由批註或流暢 API 定義的驗證、在中`IValidatableObject`建立的驗證 ( `Blog.Validate`例如), 以及在`DbContext.ValidateEntity`方法中執行的驗證。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-167">`DbContext.GetValidationErrors` will trigger all of the validations, those defined by annotations or the Fluent API, the validation created in `IValidatableObject` (for example, `Blog.Validate`), and the validations performed in the `DbContext.ValidateEntity` method.</span></span>

<span data-ttu-id="d5bc9-168">下列程式碼將會`GetValidationErrors`在目前的實例`DbContext`上呼叫。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-168">The following code will call `GetValidationErrors` on the current instance of a `DbContext`.</span></span> <span data-ttu-id="d5bc9-169">`ValidationErrors`依實體類型分組至`DbEntityValidationResult`。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-169">`ValidationErrors` are grouped by entity type into `DbEntityValidationResult`.</span></span> <span data-ttu-id="d5bc9-170">程式碼會先逐一查看`DbEntityValidationResult`方法所傳回的, 然後再逐一查看`DbValidationError`其中的每個。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-170">The code iterates first through the `DbEntityValidationResult`s returned by the method and then through each `DbValidationError` inside.</span></span>

``` csharp
foreach (var validationResult in db.GetValidationErrors())
{
    foreach (var error in validationResult.ValidationErrors)
    {
        Debug.WriteLine(
            "Entity Property: {0}, Error {1}",
            error.PropertyName,
            error.ErrorMessage);
    }
}
```

## <a name="other-considerations-when-using-validation"></a><span data-ttu-id="d5bc9-171">使用驗證時的其他考慮</span><span class="sxs-lookup"><span data-stu-id="d5bc9-171">Other considerations when using validation</span></span>

<span data-ttu-id="d5bc9-172">以下是使用 Entity Framework 驗證時要考慮的幾個重點:</span><span class="sxs-lookup"><span data-stu-id="d5bc9-172">Here are a few other points to consider when using Entity Framework validation:</span></span>

- <span data-ttu-id="d5bc9-173">在驗證期間停用消極式載入</span><span class="sxs-lookup"><span data-stu-id="d5bc9-173">Lazy loading is disabled during validation</span></span>
- <span data-ttu-id="d5bc9-174">EF 會驗證非對應屬性 (未對應至資料庫中資料行的屬性) 上的資料批註</span><span class="sxs-lookup"><span data-stu-id="d5bc9-174">EF will validate data annotations on non-mapped properties (properties that are not mapped to a column in the database)</span></span>
- <span data-ttu-id="d5bc9-175">在期間`SaveChanges`偵測到變更之後, 就會執行驗證。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-175">Validation is performed after changes are detected during `SaveChanges`.</span></span> <span data-ttu-id="d5bc9-176">如果您在驗證期間進行變更, 您必須負責通知變更追蹤程式</span><span class="sxs-lookup"><span data-stu-id="d5bc9-176">If you make changes during validation it is your responsibility to notify the change tracker</span></span>
- <span data-ttu-id="d5bc9-177">`DbUnexpectedValidationException`如果驗證期間發生錯誤, 則會擲回</span><span class="sxs-lookup"><span data-stu-id="d5bc9-177">`DbUnexpectedValidationException` is thrown if errors occur during validation</span></span>
- <span data-ttu-id="d5bc9-178">Entity Framework 包含在模型中的 facet (最大長度、必要等) 會導致驗證, 即使您的類別中沒有資料批註, 以及 (或) 您使用 EF 設計工具來建立模型</span><span class="sxs-lookup"><span data-stu-id="d5bc9-178">Facets that Entity Framework includes in the model (maximum length, required, etc.) will cause validation, even if there are no data annotations in your classes and/or you used the EF Designer to create your model</span></span>
- <span data-ttu-id="d5bc9-179">優先順序規則:</span><span class="sxs-lookup"><span data-stu-id="d5bc9-179">Precedence rules:</span></span>
  - <span data-ttu-id="d5bc9-180">流暢的 API 呼叫會覆寫對應的資料批註</span><span class="sxs-lookup"><span data-stu-id="d5bc9-180">Fluent API calls override the corresponding data annotations</span></span>
- <span data-ttu-id="d5bc9-181">執行順序:</span><span class="sxs-lookup"><span data-stu-id="d5bc9-181">Execution order:</span></span>
  - <span data-ttu-id="d5bc9-182">屬性驗證會在類型驗證之前發生</span><span class="sxs-lookup"><span data-stu-id="d5bc9-182">Property validation occurs before type validation</span></span>
  - <span data-ttu-id="d5bc9-183">只有當屬性驗證成功時, 才會進行類型驗證</span><span class="sxs-lookup"><span data-stu-id="d5bc9-183">Type validation only occurs if property validation succeeds</span></span>
- <span data-ttu-id="d5bc9-184">如果屬性很複雜, 其驗證也會包含:</span><span class="sxs-lookup"><span data-stu-id="d5bc9-184">If a property is complex, its validation will also include:</span></span>
  - <span data-ttu-id="d5bc9-185">複雜型別屬性的屬性層級驗證</span><span class="sxs-lookup"><span data-stu-id="d5bc9-185">Property-level validation on the complex type properties</span></span>
  - <span data-ttu-id="d5bc9-186">複雜型別上的型別層級`IValidatableObject`驗證, 包括複雜型別上的驗證</span><span class="sxs-lookup"><span data-stu-id="d5bc9-186">Type level validation on the complex type, including `IValidatableObject` validation on the complex type</span></span>

## <a name="summary"></a><span data-ttu-id="d5bc9-187">總結</span><span class="sxs-lookup"><span data-stu-id="d5bc9-187">Summary</span></span>

<span data-ttu-id="d5bc9-188">Entity Framework 中的驗證 API 在 MVC 中的用戶端驗證非常好用, 但是您不需要依賴用戶端驗證。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-188">The validation API in Entity Framework plays very nicely with client side validation in MVC but you don't have to rely on client-side validation.</span></span> <span data-ttu-id="d5bc9-189">Entity Framework 會針對您使用 code first 流暢 API 套用的 DataAnnotations 或設定, 負責處理伺服器端的驗證。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-189">Entity Framework will take care of the validation on the server side for DataAnnotations or configurations you've applied with the code first Fluent API.</span></span>

<span data-ttu-id="d5bc9-190">您也看到許多擴充點, 可用於自訂您是否使用`IValidatableObject`介面或點擊`DbContext.ValidateEntity`方法中的行為。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-190">You also saw a number of extensibility points for customizing the behavior whether you use the `IValidatableObject` interface or tap into the `DbContext.ValidateEntity` method.</span></span> <span data-ttu-id="d5bc9-191">而且最後兩個驗證方法是透過`DbContext`來提供, 不論您是使用 Code First、Model First 還是 Database First 工作流程來描述概念模型。</span><span class="sxs-lookup"><span data-stu-id="d5bc9-191">And these last two means of validation are available through the `DbContext`, whether you use the Code First, Model First or Database First workflow to describe your conceptual model.</span></span>
