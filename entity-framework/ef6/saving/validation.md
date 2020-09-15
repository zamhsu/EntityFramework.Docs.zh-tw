---
title: 驗證-EF6
description: Entity Framework 6 中的驗證
author: divega
ms.date: 10/23/2016
ms.openlocfilehash: 29f5e22d7f2e388d6e8271a35d10bbf6e318eafa
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073687"
---
# <a name="data-validation"></a><span data-ttu-id="528bf-103">資料驗證</span><span class="sxs-lookup"><span data-stu-id="528bf-103">Data Validation</span></span>
> [!NOTE]
> <span data-ttu-id="528bf-104">**僅限 ef 4.1** ：此頁面中所討論的功能、api 等已于 Entity Framework 4.1 中引進。</span><span class="sxs-lookup"><span data-stu-id="528bf-104">**EF4.1 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 4.1.</span></span> <span data-ttu-id="528bf-105">如果您使用的是較舊的版本，部分或全部資訊不適用</span><span class="sxs-lookup"><span data-stu-id="528bf-105">If you are using an earlier version, some or all of the information does not apply</span></span>

<span data-ttu-id="528bf-106">此頁面上的內容是由 Julie Lerman () 最初撰寫的文章所調整 [https://thedatafarm.com](https://thedatafarm.com) 。</span><span class="sxs-lookup"><span data-stu-id="528bf-106">The content on this page is adapted from an article originally written by Julie Lerman ([https://thedatafarm.com](https://thedatafarm.com)).</span></span>

<span data-ttu-id="528bf-107">Entity Framework 提供許多驗證功能，可將其饋送至使用者介面以進行用戶端驗證，或用於伺服器端驗證。</span><span class="sxs-lookup"><span data-stu-id="528bf-107">Entity Framework provides a great variety of validation features that can feed through to a user interface for client-side validation or be used for server-side validation.</span></span> <span data-ttu-id="528bf-108">使用 code first 時，您可以使用注釋或流暢的 API 設定來指定驗證。</span><span class="sxs-lookup"><span data-stu-id="528bf-108">When using code first, you can specify validations using annotation or fluent API configurations.</span></span> <span data-ttu-id="528bf-109">您可以在程式碼中指定其他驗證和更複雜的驗證，而且不論您的模型是從 code first hails、第一個模型或第一個資料庫，都能運作。</span><span class="sxs-lookup"><span data-stu-id="528bf-109">Additional validations, and more complex, can be specified in code and will work whether your model hails from code first, model first or database first.</span></span>

## <a name="the-model"></a><span data-ttu-id="528bf-110">模型</span><span class="sxs-lookup"><span data-stu-id="528bf-110">The model</span></span>

<span data-ttu-id="528bf-111">我將使用一組簡單的類別來示範驗證： Blog 和 Post。</span><span class="sxs-lookup"><span data-stu-id="528bf-111">I'll demonstrate the validations with a simple pair of classes: Blog and Post.</span></span>

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

## <a name="data-annotations"></a><span data-ttu-id="528bf-112">資料註釋</span><span class="sxs-lookup"><span data-stu-id="528bf-112">Data Annotations</span></span>

<span data-ttu-id="528bf-113">Code First 使用來自元件的注釋 `System.ComponentModel.DataAnnotations` ，作為設定 code First 類別的其中一種方式。</span><span class="sxs-lookup"><span data-stu-id="528bf-113">Code First uses annotations from the `System.ComponentModel.DataAnnotations` assembly as one means of configuring code first classes.</span></span> <span data-ttu-id="528bf-114">這些批註中有提供規則 `Required` ，例如、 `MaxLength` 和 `MinLength` 。</span><span class="sxs-lookup"><span data-stu-id="528bf-114">Among these annotations are those which provide rules such as the `Required`, `MaxLength` and `MinLength`.</span></span> <span data-ttu-id="528bf-115">許多 .NET 用戶端應用程式也會辨識這些批註，例如 ASP.NET MVC。</span><span class="sxs-lookup"><span data-stu-id="528bf-115">A number of .NET client applications also recognize these annotations, for example, ASP.NET MVC.</span></span> <span data-ttu-id="528bf-116">您可以使用這些批註來達成用戶端和伺服器端驗證。</span><span class="sxs-lookup"><span data-stu-id="528bf-116">You can achieve both client side and server side validation with these annotations.</span></span> <span data-ttu-id="528bf-117">例如，您可以強制 Blog Title 屬性成為必要的屬性。</span><span class="sxs-lookup"><span data-stu-id="528bf-117">For example, you can force the Blog Title property to be a required property.</span></span>

``` csharp
[Required]
public string Title { get; set; }
```

<span data-ttu-id="528bf-118">在應用程式中沒有任何額外的程式碼或標記變更時，現有的 MVC 應用程式將會執行用戶端驗證，甚至使用屬性和批註名稱動態建立訊息。</span><span class="sxs-lookup"><span data-stu-id="528bf-118">With no additional code or markup changes in the application, an existing MVC application will perform client side validation, even dynamically building a message using the property and annotation names.</span></span>

![圖 1](~/ef6/media/figure01.png)

<span data-ttu-id="528bf-120">在此建立視圖的 post 方法中，Entity Framework 是用來將新的 blog 儲存至資料庫，但 MVC 的用戶端驗證會在應用程式到達該程式碼之前觸發。</span><span class="sxs-lookup"><span data-stu-id="528bf-120">In the post back method of this Create view, Entity Framework is used to save the new blog to the database, but MVC's client-side validation is triggered before the application reaches that code.</span></span>

<span data-ttu-id="528bf-121">用戶端驗證並不是專案符號。</span><span class="sxs-lookup"><span data-stu-id="528bf-121">Client side validation is not bullet-proof however.</span></span> <span data-ttu-id="528bf-122">使用者可能會影響瀏覽器的功能或更糟的功能，駭客可能會使用某些 trickery 來避免 UI 驗證。</span><span class="sxs-lookup"><span data-stu-id="528bf-122">Users can impact features of their browser or worse yet, a hacker might use some trickery to avoid the UI validations.</span></span> <span data-ttu-id="528bf-123">但 Entity Framework 也會辨識 `Required` 注釋並進行驗證。</span><span class="sxs-lookup"><span data-stu-id="528bf-123">But Entity Framework will also recognize the `Required` annotation and validate it.</span></span>

<span data-ttu-id="528bf-124">測試這項工作的一個簡單方式是停用 MVC 的用戶端驗證功能。</span><span class="sxs-lookup"><span data-stu-id="528bf-124">A simple way to test this is to disable MVC's client-side validation feature.</span></span> <span data-ttu-id="528bf-125">您可以在 MVC 應用程式的 web.config 檔中進行這項作業。</span><span class="sxs-lookup"><span data-stu-id="528bf-125">You can do this in the MVC application's web.config file.</span></span> <span data-ttu-id="528bf-126">AppSettings 區段有 ClientValidationEnabled 的索引鍵。</span><span class="sxs-lookup"><span data-stu-id="528bf-126">The appSettings section has a key for ClientValidationEnabled.</span></span> <span data-ttu-id="528bf-127">將此機碼設為 false，會讓 UI 無法執行驗證。</span><span class="sxs-lookup"><span data-stu-id="528bf-127">Setting this key to false will prevent the UI from performing validations.</span></span>

``` xml
<appSettings>
    <add key="ClientValidationEnabled"value="false"/>
    ...
</appSettings>
```

<span data-ttu-id="528bf-128">即使已停用用戶端驗證，您仍會在應用程式中取得相同的回應。</span><span class="sxs-lookup"><span data-stu-id="528bf-128">Even with the client-side validation disabled, you will get the same response in your application.</span></span> <span data-ttu-id="528bf-129">錯誤訊息「需要標題欄位」會顯示為先前。</span><span class="sxs-lookup"><span data-stu-id="528bf-129">The error message "The Title field is required" will be displayed as before.</span></span> <span data-ttu-id="528bf-130">但現在會是伺服器端驗證的結果。</span><span class="sxs-lookup"><span data-stu-id="528bf-130">Except now it will be a result of server-side validation.</span></span> <span data-ttu-id="528bf-131">Entity Framework 會先在批註 (上執行驗證， `Required` 再 bothers 建立 `INSERT` 傳送至) 資料庫的命令，並將錯誤傳回至將顯示訊息的 MVC。</span><span class="sxs-lookup"><span data-stu-id="528bf-131">Entity Framework will perform the validation on the `Required` annotation (before it even bothers to build an `INSERT` command to send to the database) and return the error to MVC which will display the message.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="528bf-132">Fluent API</span><span class="sxs-lookup"><span data-stu-id="528bf-132">Fluent API</span></span>

<span data-ttu-id="528bf-133">您可以使用 code first 的流暢 API （而非注釋）來取得相同的用戶端 & 伺服器端驗證。</span><span class="sxs-lookup"><span data-stu-id="528bf-133">You can use code first's fluent API instead of annotations to get the same client side & server side validation.</span></span> <span data-ttu-id="528bf-134">`Required`我會使用 MaxLength 驗證來說明這一點，而不是使用。</span><span class="sxs-lookup"><span data-stu-id="528bf-134">Rather than use `Required`, I'll show you this using a MaxLength validation.</span></span>

<span data-ttu-id="528bf-135">在程式碼第一次從類別建立模型時，會套用流暢的 API 設定。</span><span class="sxs-lookup"><span data-stu-id="528bf-135">Fluent API configurations are applied as code first is building the model from the classes.</span></span> <span data-ttu-id="528bf-136">您可以藉由覆寫 DbCoNtext 類別的 OnModelCreating 方法來插入設定。</span><span class="sxs-lookup"><span data-stu-id="528bf-136">You can inject the configurations by overriding the DbContext class' OnModelCreating  method.</span></span> <span data-ttu-id="528bf-137">以下是一種設定，指定 BloggerName 屬性不能超過10個字元。</span><span class="sxs-lookup"><span data-stu-id="528bf-137">Here is a configuration specifying that the BloggerName property can be no longer than 10 characters.</span></span>

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

<span data-ttu-id="528bf-138">根據流暢的 API 設定所擲回的驗證錯誤不會自動到達 UI，但您可以在程式碼中加以捕捉，然後據以回應。</span><span class="sxs-lookup"><span data-stu-id="528bf-138">Validation errors thrown based on the Fluent API configurations will not automatically reach the UI, but you can capture it in code and then respond to it accordingly.</span></span>

<span data-ttu-id="528bf-139">以下是應用程式的 BlogController 類別中的一些例外狀況處理錯誤碼，當 Entity Framework 嘗試使用超過10個字元的 BloggerName 來儲存 blog 時，會捕捉該驗證錯誤。</span><span class="sxs-lookup"><span data-stu-id="528bf-139">Here's some exception handling error code in the application's BlogController class that captures that validation error when Entity Framework attempts to save a blog with a BloggerName that exceeds the 10 character maximum.</span></span>

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

<span data-ttu-id="528bf-140">驗證不會自動傳回給視圖，這也是使用中其他程式碼的原因 `ModelState.AddModelError` 。</span><span class="sxs-lookup"><span data-stu-id="528bf-140">The validation doesn't automatically get passed back into the view which is why the additional code that uses `ModelState.AddModelError` is being used.</span></span> <span data-ttu-id="528bf-141">這可確保錯誤詳細資料會將它變成視圖，然後使用 `ValidationMessageFor` Htmlhelper 來顯示錯誤。</span><span class="sxs-lookup"><span data-stu-id="528bf-141">This ensures that the error details make it to the view which will then use the `ValidationMessageFor` Htmlhelper to display the error.</span></span>

``` csharp
@Html.ValidationMessageFor(model => model.BloggerName)
```

## <a name="ivalidatableobject"></a><span data-ttu-id="528bf-142">IValidatableObject</span><span class="sxs-lookup"><span data-stu-id="528bf-142">IValidatableObject</span></span>

<span data-ttu-id="528bf-143">`IValidatableObject` 是存在的介面 `System.ComponentModel.DataAnnotations` 。</span><span class="sxs-lookup"><span data-stu-id="528bf-143">`IValidatableObject` is an interface that lives in `System.ComponentModel.DataAnnotations`.</span></span> <span data-ttu-id="528bf-144">雖然它不是 Entity Framework API 的一部分，但是您仍然可以在 Entity Framework 類別中利用它進行伺服器端驗證。</span><span class="sxs-lookup"><span data-stu-id="528bf-144">While it is not part of the Entity Framework API, you can still leverage it for server-side validation in your Entity Framework classes.</span></span> <span data-ttu-id="528bf-145">`IValidatableObject` 提供 `Validate` Entity Framework 在 SaveChanges 期間呼叫的方法，或您可以在每次想要驗證類別時呼叫。</span><span class="sxs-lookup"><span data-stu-id="528bf-145">`IValidatableObject` provides a `Validate` method that Entity Framework will call during SaveChanges or you can call yourself any time you want to validate the classes.</span></span>

<span data-ttu-id="528bf-146">`Required`的設定，以及 `MaxLength` 在單一欄位上執行驗證。</span><span class="sxs-lookup"><span data-stu-id="528bf-146">Configurations such as `Required` and `MaxLength` perform validation on a single field.</span></span> <span data-ttu-id="528bf-147">在方法中， `Validate` 您可以有更複雜的邏輯，例如，比較兩個欄位。</span><span class="sxs-lookup"><span data-stu-id="528bf-147">In the `Validate` method you can have even more complex logic, for example, comparing two fields.</span></span>

<span data-ttu-id="528bf-148">在下列範例中， `Blog` 類別已擴充為可執行， `IValidatableObject` 然後提供 `Title` `BloggerName` 不符合的規則。</span><span class="sxs-lookup"><span data-stu-id="528bf-148">In the following example, the `Blog` class has been extended to implement `IValidatableObject` and then provide a rule that the `Title` and `BloggerName` cannot match.</span></span>

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

<span data-ttu-id="528bf-149">此函式 `ValidationResult` 會採用 `string` 代表錯誤訊息的，以及表示 `string` 與驗證相關聯之成員名稱的陣列。</span><span class="sxs-lookup"><span data-stu-id="528bf-149">The `ValidationResult` constructor takes a `string` that represents the error message and an array of `string`s that represent the member names that are associated with the validation.</span></span> <span data-ttu-id="528bf-150">由於這項驗證會同時檢查 `Title` 和 `BloggerName` ，因此會傳回兩個屬性名稱。</span><span class="sxs-lookup"><span data-stu-id="528bf-150">Since this validation checks both the `Title` and the `BloggerName`, both property names are returned.</span></span>

<span data-ttu-id="528bf-151">不同于流暢 API 所提供的驗證，此驗證結果將會由視圖辨識，而且我稍早用來將錯誤新增至的例外狀況處理常式 `ModelState` 是不必要的。</span><span class="sxs-lookup"><span data-stu-id="528bf-151">Unlike the validation provided by the Fluent API, this validation result will be recognized by the View and the exception handler that I used earlier to add the error into `ModelState` is unnecessary.</span></span> <span data-ttu-id="528bf-152">因為我在中同時設定了這兩個屬性名稱 `ValidationResult` ，所以 MVC HtmlHelpers 會顯示這兩個屬性的錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="528bf-152">Because I set both property names in the `ValidationResult`, the MVC HtmlHelpers display the error message for both of those properties.</span></span>

![圖 2](~/ef6/media/figure02.png)

## <a name="dbcontextvalidateentity"></a><span data-ttu-id="528bf-154">DbCoNtext. ValidateEntity</span><span class="sxs-lookup"><span data-stu-id="528bf-154">DbContext.ValidateEntity</span></span>

<span data-ttu-id="528bf-155">`DbContext` 具有稱為的可覆寫方法 `ValidateEntity` 。</span><span class="sxs-lookup"><span data-stu-id="528bf-155">`DbContext` has an overridable method called `ValidateEntity`.</span></span> <span data-ttu-id="528bf-156">當您呼叫時 `SaveChanges` ，Entity Framework 會針對其狀態為非的快取中的每個實體呼叫這個方法 `Unchanged` 。</span><span class="sxs-lookup"><span data-stu-id="528bf-156">When you call `SaveChanges`, Entity Framework will call this method for each entity in its cache whose state is not `Unchanged`.</span></span> <span data-ttu-id="528bf-157">您可以直接在這裡放置驗證邏輯，也可以使用這個方法來呼叫，例如，在 `Blog.Validate` 上一節中加入的方法。</span><span class="sxs-lookup"><span data-stu-id="528bf-157">You can put validation logic directly in here or even use this method to call, for example, the `Blog.Validate` method added in the previous section.</span></span>

<span data-ttu-id="528bf-158">以下是 `ValidateEntity` 會驗證新的覆寫， `Post` 以確保貼文標題尚未使用的覆寫範例。</span><span class="sxs-lookup"><span data-stu-id="528bf-158">Here's an example of a `ValidateEntity` override that validates new `Post`s to ensure that the post title hasn't been used already.</span></span> <span data-ttu-id="528bf-159">它會先檢查實體是否為 post，以及其狀態是否已加入。</span><span class="sxs-lookup"><span data-stu-id="528bf-159">It first checks to see if the entity is a post and that its state is Added.</span></span> <span data-ttu-id="528bf-160">如果是這種情況，則會在資料庫中查看是否已有相同標題的貼文。</span><span class="sxs-lookup"><span data-stu-id="528bf-160">If that's the case, then it looks in the database to see if there is already a post with the same title.</span></span> <span data-ttu-id="528bf-161">如果已有現有的貼文，則 `DbEntityValidationResult` 會建立新的。</span><span class="sxs-lookup"><span data-stu-id="528bf-161">If there is an existing post already, then a new `DbEntityValidationResult` is created.</span></span>

<span data-ttu-id="528bf-162">`DbEntityValidationResult` 裝載 `DbEntityEntry` `ICollection<DbValidationErrors>` 單一實體的和。</span><span class="sxs-lookup"><span data-stu-id="528bf-162">`DbEntityValidationResult` houses a `DbEntityEntry` and an `ICollection<DbValidationErrors>` for a single entity.</span></span> <span data-ttu-id="528bf-163">在這個方法的開頭，會具 `DbEntityValidationResult` 現化，然後將探索到的任何錯誤加入其 `ValidationErrors` 集合中。</span><span class="sxs-lookup"><span data-stu-id="528bf-163">At the start of this method, a `DbEntityValidationResult` is instantiated and then any errors that are discovered are added into its `ValidationErrors` collection.</span></span>

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

## <a name="explicitly-triggering-validation"></a><span data-ttu-id="528bf-164">明確觸發驗證</span><span class="sxs-lookup"><span data-stu-id="528bf-164">Explicitly triggering validation</span></span>

<span data-ttu-id="528bf-165">的呼叫會 `SaveChanges` 觸發本文涵蓋的所有驗證。</span><span class="sxs-lookup"><span data-stu-id="528bf-165">A call to `SaveChanges` triggers all of the validations covered in this article.</span></span> <span data-ttu-id="528bf-166">但是您不需要依賴 `SaveChanges` 。</span><span class="sxs-lookup"><span data-stu-id="528bf-166">But you don't need to rely on `SaveChanges`.</span></span> <span data-ttu-id="528bf-167">您可能會想要在應用程式中的其他地方進行驗證。</span><span class="sxs-lookup"><span data-stu-id="528bf-167">You may prefer to validate elsewhere in your application.</span></span>

<span data-ttu-id="528bf-168">`DbContext.GetValidationErrors` 將會觸發所有驗證（由批註或流暢的 API 定義）、在 (中建立的驗證（ `IValidatableObject` 例如 `Blog.Validate`) ），以及在方法中執行的驗證 `DbContext.ValidateEntity` 。</span><span class="sxs-lookup"><span data-stu-id="528bf-168">`DbContext.GetValidationErrors` will trigger all of the validations, those defined by annotations or the Fluent API, the validation created in `IValidatableObject` (for example, `Blog.Validate`), and the validations performed in the `DbContext.ValidateEntity` method.</span></span>

<span data-ttu-id="528bf-169">下列程式碼會 `GetValidationErrors` 在目前的實例上呼叫 `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="528bf-169">The following code will call `GetValidationErrors` on the current instance of a `DbContext`.</span></span> <span data-ttu-id="528bf-170">`ValidationErrors` 會依實體類型分組至 `DbEntityValidationResult` 。</span><span class="sxs-lookup"><span data-stu-id="528bf-170">`ValidationErrors` are grouped by entity type into `DbEntityValidationResult`.</span></span> <span data-ttu-id="528bf-171">程式碼會先逐一查看 `DbEntityValidationResult` 方法所傳回的，然後再逐一查看 `DbValidationError` 。</span><span class="sxs-lookup"><span data-stu-id="528bf-171">The code iterates first through the `DbEntityValidationResult`s returned by the method and then through each `DbValidationError` inside.</span></span>

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

## <a name="other-considerations-when-using-validation"></a><span data-ttu-id="528bf-172">使用驗證時的其他考慮</span><span class="sxs-lookup"><span data-stu-id="528bf-172">Other considerations when using validation</span></span>

<span data-ttu-id="528bf-173">以下是使用 Entity Framework 驗證時要考慮的一些其他幾點：</span><span class="sxs-lookup"><span data-stu-id="528bf-173">Here are a few other points to consider when using Entity Framework validation:</span></span>

- <span data-ttu-id="528bf-174">在驗證期間停用延遲載入</span><span class="sxs-lookup"><span data-stu-id="528bf-174">Lazy loading is disabled during validation</span></span>
- <span data-ttu-id="528bf-175">EF 會驗證非對應屬性的資料批註 (未對應到資料庫中資料行的屬性) </span><span class="sxs-lookup"><span data-stu-id="528bf-175">EF will validate data annotations on non-mapped properties (properties that are not mapped to a column in the database)</span></span>
- <span data-ttu-id="528bf-176">在期間偵測到變更之後，就會執行驗證 `SaveChanges` 。</span><span class="sxs-lookup"><span data-stu-id="528bf-176">Validation is performed after changes are detected during `SaveChanges`.</span></span> <span data-ttu-id="528bf-177">如果您在驗證期間進行變更，您必須負責通知變更追蹤器</span><span class="sxs-lookup"><span data-stu-id="528bf-177">If you make changes during validation it is your responsibility to notify the change tracker</span></span>
- <span data-ttu-id="528bf-178">`DbUnexpectedValidationException` 如果驗證期間發生錯誤，則會擲回</span><span class="sxs-lookup"><span data-stu-id="528bf-178">`DbUnexpectedValidationException` is thrown if errors occur during validation</span></span>
- <span data-ttu-id="528bf-179">Entity Framework 包含在模型中的 facet (最大長度、必要項等等。即使您的類別和/或您使用 EF 設計工具建立模型，) 也會導致驗證</span><span class="sxs-lookup"><span data-stu-id="528bf-179">Facets that Entity Framework includes in the model (maximum length, required, etc.) will cause validation, even if there are no data annotations in your classes and/or you used the EF Designer to create your model</span></span>
- <span data-ttu-id="528bf-180">優先順序規則：</span><span class="sxs-lookup"><span data-stu-id="528bf-180">Precedence rules:</span></span>
  - <span data-ttu-id="528bf-181">流暢的 API 呼叫會覆寫對應的資料批註</span><span class="sxs-lookup"><span data-stu-id="528bf-181">Fluent API calls override the corresponding data annotations</span></span>
- <span data-ttu-id="528bf-182">執行順序：</span><span class="sxs-lookup"><span data-stu-id="528bf-182">Execution order:</span></span>
  - <span data-ttu-id="528bf-183">屬性驗證會在類型驗證之前發生</span><span class="sxs-lookup"><span data-stu-id="528bf-183">Property validation occurs before type validation</span></span>
  - <span data-ttu-id="528bf-184">只有在屬性驗證成功時才會進行類型驗證</span><span class="sxs-lookup"><span data-stu-id="528bf-184">Type validation only occurs if property validation succeeds</span></span>
- <span data-ttu-id="528bf-185">如果屬性很複雜，其驗證也將包含：</span><span class="sxs-lookup"><span data-stu-id="528bf-185">If a property is complex, its validation will also include:</span></span>
  - <span data-ttu-id="528bf-186">複雜型別屬性上的屬性層級驗證</span><span class="sxs-lookup"><span data-stu-id="528bf-186">Property-level validation on the complex type properties</span></span>
  - <span data-ttu-id="528bf-187">複雜型別的型別層級驗證，包括 `IValidatableObject` 複雜型別上的驗證</span><span class="sxs-lookup"><span data-stu-id="528bf-187">Type level validation on the complex type, including `IValidatableObject` validation on the complex type</span></span>

## <a name="summary"></a><span data-ttu-id="528bf-188">摘要</span><span class="sxs-lookup"><span data-stu-id="528bf-188">Summary</span></span>

<span data-ttu-id="528bf-189">Entity Framework 中的驗證 API 與 MVC 中的用戶端驗證非常完美，但您不需要依賴用戶端驗證。</span><span class="sxs-lookup"><span data-stu-id="528bf-189">The validation API in Entity Framework plays very nicely with client side validation in MVC but you don't have to rely on client-side validation.</span></span> <span data-ttu-id="528bf-190">Entity Framework 將會處理伺服器端的驗證，以取得您已使用 code first 流暢 API 套用的 DataAnnotations 或設定。</span><span class="sxs-lookup"><span data-stu-id="528bf-190">Entity Framework will take care of the validation on the server side for DataAnnotations or configurations you've applied with the code first Fluent API.</span></span>

<span data-ttu-id="528bf-191">您也看到許多自訂行為的擴充點，可用於自訂您是否使用 `IValidatableObject` 介面或利用 `DbContext.ValidateEntity` 方法。</span><span class="sxs-lookup"><span data-stu-id="528bf-191">You also saw a number of extensibility points for customizing the behavior whether you use the `IValidatableObject` interface or tap into the `DbContext.ValidateEntity` method.</span></span> <span data-ttu-id="528bf-192">`DbContext`無論您使用 Code First、Model First 或 Database First 工作流程來描述您的概念模型，都可以透過來使用最後兩種驗證方式。</span><span class="sxs-lookup"><span data-stu-id="528bf-192">And these last two means of validation are available through the `DbContext`, whether you use the Code First, Model First or Database First workflow to describe your conceptual model.</span></span>
