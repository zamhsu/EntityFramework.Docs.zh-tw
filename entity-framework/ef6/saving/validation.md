---
title: 驗證-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 77d6a095-c0d0-471e-80b9-8f9aea6108b2
ms.openlocfilehash: 3aeb33763819544618c4a3068bb278c9b23409b6
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2018
ms.locfileid: "45490625"
---
# <a name="data-validation"></a><span data-ttu-id="d9ac0-102">資料驗證</span><span class="sxs-lookup"><span data-stu-id="d9ac0-102">Data Validation</span></span>
> [!NOTE]
> <span data-ttu-id="d9ac0-103">**EF4.1 及更新版本僅**-功能、 Api、 Entity Framework 4.1 中導入等本頁所述。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-103">**EF4.1 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 4.1.</span></span> <span data-ttu-id="d9ac0-104">如果您使用較早版本，部分或全部的資訊不適用</span><span class="sxs-lookup"><span data-stu-id="d9ac0-104">If you are using an earlier version, some or all of the information does not apply</span></span>

<span data-ttu-id="d9ac0-105">此頁面上的內容是來自和文章作者： Julie Lerman 原始撰寫 ([http://thedatafarm.com](http://thedatafarm.com))。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-105">The content on this page is adapted from and article originally written by Julie Lerman ([http://thedatafarm.com](http://thedatafarm.com)).</span></span>

<span data-ttu-id="d9ac0-106">Entity Framework 提供許多驗證功能，可以透過餵送到用戶端驗證的使用者介面，或用於伺服器端驗證。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-106">Entity Framework provides a great variety of validation features that can feed through to a user interface for client-side validation or be used for server-side validation.</span></span> <span data-ttu-id="d9ac0-107">當第一次使用的程式碼，您可以指定使用註解或 fluent API 組態的驗證。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-107">When using code first, you can specify validations using annotation or fluent API configurations.</span></span> <span data-ttu-id="d9ac0-108">額外的驗證，以及更複雜，可以指定在程式碼中，並可是否模型在程式碼來自第一次，第一次模型或資料庫第一次。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-108">Additional validations, and more complex, can be specified in code and will work whether your model hails from code first, model first or database first.</span></span>

## <a name="the-model"></a><span data-ttu-id="d9ac0-109">模型</span><span class="sxs-lookup"><span data-stu-id="d9ac0-109">The model</span></span>

<span data-ttu-id="d9ac0-110">我將示範如何使用類別的簡單組驗證： 部落格和文章。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-110">I’ll demonstrate the validations with a simple pair of classes: Blog and Post.</span></span>

``` csharp
    public class Blog
      {
          public int Id { get; set; }
          public string Title { get; set; }
          public string BloggerName { get; set; }
          public DateTime DateCreated { get; set; }
          public virtual ICollection<Post> Posts { get; set; }
          }
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
## <a name="data-annotations"></a><span data-ttu-id="d9ac0-111">資料註釋</span><span class="sxs-lookup"><span data-stu-id="d9ac0-111">Data Annotations</span></span>

<span data-ttu-id="d9ac0-112">首先，程式碼會使用從 System.ComponentModel.DataAnnotations 組件的註解做為設定程式碼的第一個類別的一個方法。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-112">Code First uses annotations from the System.ComponentModel.DataAnnotations assembly as one means of configuring code first classes.</span></span> <span data-ttu-id="d9ac0-113">這些註解之間所提供的規則，例如 「 必要 」 的 MaxLength 及 MinLength。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-113">Among these annotations are those which provide rules such as the Required, MaxLength and MinLength.</span></span> <span data-ttu-id="d9ac0-114">.NET 用戶端應用程式數目也會辨識這些註解，例如，ASP.NET MVC。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-114">A number of .NET client applications also recognize these annotations, for example, ASP.NET MVC.</span></span> <span data-ttu-id="d9ac0-115">您可以達到這些註解這兩個用戶端和伺服器端驗證。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-115">You can achieve both client side and server side validation with these annotations.</span></span> <span data-ttu-id="d9ac0-116">例如，您可以強制的部落格 Title 屬性是必要的屬性。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-116">For example, you can force the Blog Title property to be a required property.</span></span>

``` csharp
    [Required]
    public string Title { get; set; }
```

<span data-ttu-id="d9ac0-117">沒有額外的程式碼或標記變更應用程式中的，現有的 MVC 應用程式會執行用戶端驗證，甚至以動態方式建置訊息使用的屬性和註釋名稱。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-117">With no additional code or markup changes in the application, an existing MVC application will perform client side validation, even dynamically building a message using the property and annotation names.</span></span>

![圖 1](~/ef6/media/figure01.png)

<span data-ttu-id="d9ac0-119">貼文中傳回的這個建立檢視的方法，將新的部落格儲存至資料庫中，使用 Entity Framework，但 MVC 的用戶端驗證，就會觸發應用程式到達該程式碼之前。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-119">In the post back method of this Create view, Entity Framework is used to save the new blog to the database, but MVC’s client-side validation is triggered before the application reaches that code.</span></span>

<span data-ttu-id="d9ac0-120">用戶端驗證不過並不確實。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-120">Client side validation is not bullet-proof however.</span></span> <span data-ttu-id="d9ac0-121">使用者可能會影響其瀏覽器的功能，或更糟的是，駭客可能會使用一些以前來避免 UI 驗證。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-121">Users can impact features of their browser or worse yet, a hacker might use some trickery to avoid the UI validations.</span></span> <span data-ttu-id="d9ac0-122">但 Entity Framework 也會識別所需的註解並加以驗證。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-122">But Entity Framework will also recognize the Required annotation and validate it.</span></span>

<span data-ttu-id="d9ac0-123">若要測試此簡單的方法是停用 MVC 的用戶端驗證功能。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-123">A simple way to test this is to disable MVC’s client-side validation feature.</span></span> <span data-ttu-id="d9ac0-124">在 MVC 應用程式的 web.config 檔案中，您可以執行這項操作。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-124">You can do this in the MVC application’s web.config file.</span></span> <span data-ttu-id="d9ac0-125">AppSettings 區段的 ClientValidationEnabled 有索引鍵。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-125">The appSettings section has a key for ClientValidationEnabled.</span></span> <span data-ttu-id="d9ac0-126">將這個機碼設定為 false 會阻礙 UI 執行驗證。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-126">Setting this key to false will prevent the UI from performing validations.</span></span>

``` xml
    <appSettings>
        <add key="ClientValidationEnabled"value="false"/>
        ...
    </appSettings>
```

<span data-ttu-id="d9ac0-127">即使使用停用用戶端驗證，您會收到相同的回應，應用程式中。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-127">Even with the client-side validation disabled, you will get the same response in your application.</span></span> <span data-ttu-id="d9ac0-128">將顯示錯誤訊息 「 [標題] 欄位是必要 」，為。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-128">The error message “The Title field is required” will be displayed as.</span></span> <span data-ttu-id="d9ac0-129">但現在它將會是伺服器端驗證的結果。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-129">Except now it will be a result of server-side validation.</span></span> <span data-ttu-id="d9ac0-130">Entity Framework 將會執行驗證所需的註解 （之前它甚至困擾，建置並傳送至資料庫的 INSERT 命令），mvc 會顯示訊息，這會傳回錯誤。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-130">Entity Framework will perform the validation on the Required annotation (before it even bothers to build and INSERT command to send to the database) and return the error to MVC which will display the message.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="d9ac0-131">Fluent API</span><span class="sxs-lookup"><span data-stu-id="d9ac0-131">Fluent API</span></span>

<span data-ttu-id="d9ac0-132">您可以使用程式碼第一的 fluent API 而不是註解來取得相同的用戶端戶端和伺服器端驗證。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-132">You can use code first’s fluent API instead of annotations to get the same client side & server side validation.</span></span> <span data-ttu-id="d9ac0-133">而不是必要的使用，我將告訴您此使用 MaxLength 驗證。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-133">Rather than use Required, I’ll show you this using a MaxLength validation.</span></span>

<span data-ttu-id="d9ac0-134">Fluent API 組態會套用程式碼第一次建立模型的類別。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-134">Fluent API configurations are applied as code first is building the model from the classes.</span></span> <span data-ttu-id="d9ac0-135">您可以藉由覆寫 DbContext 類別的 OnModelCreating 方法插入的設定。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-135">You can inject the configurations by overriding the DbContext class’ OnModelCreating  method.</span></span> <span data-ttu-id="d9ac0-136">以下是設定，指定 BloggerName 屬性可以不超過 10 個字元。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-136">Here is a configuration specifying that the BloggerName property can be no longer than 10 characters.</span></span>

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

<span data-ttu-id="d9ac0-137">驗證錯誤擲回根據 Fluent API 組態不會自動 reach UI，但您可以擷取它在程式碼，然後回應據此。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-137">Validation errors thrown based on the Fluent API configurations will not automatically reach the UI, but you can capture it in code and then respond to it accordingly.</span></span>

<span data-ttu-id="d9ac0-138">這裡的一些例外狀況處理 Entity Framework 會嘗試使用超過 10 個字元的最大 BloggerName 儲存的部落格時，會擷取該驗證錯誤的應用程式的 BlogController 類別中的錯誤程式碼。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-138">Here’s some exception handling error code in the application’s BlogController class that captures that validation error when Entity Framework attempts to save a blog with a BloggerName that exceeds the 10 character maximum.</span></span>

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
        catch(DbEntityValidationException ex)
        {
            var error = ex.EntityValidationErrors.First().ValidationErrors.First();
            this.ModelState.AddModelError(error.PropertyName, error.ErrorMessage);
            return View();
        }
    }
```

<span data-ttu-id="d9ac0-139">驗證不會自動傳遞回這就是為什麼使用 ModelState.AddModelError 的其他程式碼正在使用的檢視。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-139">The validation doesn’t automatically get passed back into the view which is why the additional code that uses ModelState.AddModelError is being used.</span></span> <span data-ttu-id="d9ac0-140">這可確保錯誤詳細資料，對該檢視會接著用來顯示錯誤的 ValidationMessageFor Htmlhelper 進行它。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-140">This ensures that the error details make it to the view which will then use the ValidationMessageFor Htmlhelper to display the error.</span></span>

``` csharp
    @Html.ValidationMessageFor(model => model.BloggerName)
```

## <a name="ivalidatableobject"></a><span data-ttu-id="d9ac0-141">IValidatableObject</span><span class="sxs-lookup"><span data-stu-id="d9ac0-141">IValidatableObject</span></span>

<span data-ttu-id="d9ac0-142">IValidatableObject 是居住在 System.ComponentModel.DataAnnotations 的介面。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-142">IValidatableObject is an interface that lives in System.ComponentModel.DataAnnotations.</span></span> <span data-ttu-id="d9ac0-143">雖然它不是 Entity Framework API 的一部分，您仍然可以利用它的伺服器端驗證 Entity Framework 類別中。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-143">While it is not part of the Entity Framework API, you can still leverage it for server-side validation in your Entity Framework classes.</span></span> <span data-ttu-id="d9ac0-144">IValidatableObject 提供 Entity Framework 會呼叫期間 SaveChanges 或您的驗證方法可以自行呼叫任何您想要驗證之類別的時間。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-144">IValidatableObject provides a Validate method that Entity Framework will call during SaveChanges or you can call yourself any time you want to validate the classes.</span></span>

<span data-ttu-id="d9ac0-145">如需設定和 MaxLength 執行驗證的單一欄位上。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-145">Configurations such as Required and MaxLength perform validaton on a single field.</span></span> <span data-ttu-id="d9ac0-146">您可以驗證方法中有更複雜的邏輯，例如，比較兩個欄位。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-146">In the Validate method you can have even more complex logic, for example, comparing two fields.</span></span>

<span data-ttu-id="d9ac0-147">在下列範例中，部落格類別已擴充到實作 IValidatableObject，，然後提供 標題 和 BloggerName 無法比對的規則。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-147">In the following example, the Blog class has been extended to implement IValidatableObject and then provide a rule that the Title and BloggerName cannot match.</span></span>

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
                 yield return new ValidationResult
                  ("Blog Title cannot match Blogger Name", new[] { "Title", “BloggerName” });
             }
         }
     }
```

<span data-ttu-id="d9ac0-148">ValidationResult 建構函式會接受字串，表示錯誤訊息和表示與驗證相關聯之成員名稱的字串陣列。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-148">The ValidationResult constructor takes a string that represents the error message and an array of strings that represent the member names that are associated with the validation.</span></span> <span data-ttu-id="d9ac0-149">由於這項驗證會檢查在標題和 BloggerName，則會傳回兩個屬性名稱。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-149">Since this validation checks both the Title and the BloggerName, both property names are returned.</span></span>

<span data-ttu-id="d9ac0-150">不同於 Fluent API 所提供的驗證，此驗證的結果也會辨識由檢視而且我稍早用來加入 ModelState 錯誤的例外狀況處理常式是不必要。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-150">Unlike the validation provided by the Fluent API, this validation result will be recognized by the View and the exception handler that I used earlier to add the error into ModelState is unnecessary.</span></span> <span data-ttu-id="d9ac0-151">因為在 ValidationResult 中設定這兩個屬性名稱，MVC HtmlHelpers 會顯示錯誤訊息，這兩個屬性。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-151">Because I set both property names in the ValidationResult, the MVC HtmlHelpers display the error message for both of those properties.</span></span>

![圖 2](~/ef6/media/figure02.png)

## <a name="dbcontextvalidateentity"></a><span data-ttu-id="d9ac0-153">DbContext.ValidateEntity</span><span class="sxs-lookup"><span data-stu-id="d9ac0-153">DbContext.ValidateEntity</span></span>

<span data-ttu-id="d9ac0-154">DbContext 有呼叫 ValidateEntity 覆寫的方法。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-154">DbContext has an Overridable method called ValidateEntity.</span></span> <span data-ttu-id="d9ac0-155">當您呼叫 SaveChanges 時，Entity Framework 會呼叫這個方法，其狀態不是不變的快取中每個實體。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-155">When you call SaveChanges, Entity Framework will call this method for each entity in its cache whose state is not Unchanged.</span></span> <span data-ttu-id="d9ac0-156">您可以直接在這裡或甚至是使用中放置驗證邏輯，這個方法來呼叫，比方說，在上一節中新增 Blog.Validate 方法。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-156">You can put validation logic directly in here or even use this method to call, for example, the Blog.Validate method added in the previous section.</span></span>

<span data-ttu-id="d9ac0-157">以下是驗證以確保文章標題尚未已使用的新貼文的 ValidateEntity 覆寫的範例。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-157">Here’s an example of a ValidateEntity override that validates new Posts to ensure that the post title hasn’t been used already.</span></span> <span data-ttu-id="d9ac0-158">它首先會檢查實體是否某篇文章和其狀態會加入。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-158">It first checks to see if the entity is a post and that its state is Added.</span></span> <span data-ttu-id="d9ac0-159">如果是這樣，看起來在資料庫中，以查看是否有相同標題的文章。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-159">If that’s the case, then it looks in the database to see if there is already a post with the same title.</span></span> <span data-ttu-id="d9ac0-160">如果已經存在的文章，則會建立新的 DbEntityValidationResult。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-160">If there is an existing post already, then a new DbEntityValidationResult is created.</span></span>

<span data-ttu-id="d9ac0-161">DbEntityValidationResult 裝載 DbEntityEntry 和 ICollection 的 DbValidationErrors 單一實體。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-161">DbEntityValidationResult houses a DbEntityEntry and an ICollection of DbValidationErrors for a single entity.</span></span> <span data-ttu-id="d9ac0-162">在開始這個方法時，DbEntityValidationResult 具現化，並再探索到的任何錯誤會加入其 ValidationErrors 集合。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-162">At the start of this method, a  DbEntityValidationResult is instantiated and then any errors that are discovered are added into its ValidationErrors collection.</span></span>

``` csharp
    protected override DbEntityValidationResult ValidateEntity (
        System.Data.Entity.Infrastructure.DbEntityEntry entityEntry,
        IDictionary\<object, object> items)
    {
        var result = new DbEntityValidationResult(entityEntry, new List<DbValidationError>());
        if (entityEntry.Entity is Post && entityEntry.State == EntityState.Added)
        {
            Post post = entityEntry.Entity as Post;
            //check for uniqueness of post title
            if (Posts.Where(p => p.Title == post.Title).Count() > 0)
            {
                result.ValidationErrors.Add(
                        new System.Data.Entity.Validation.DbValidationError("Title",
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

## <a name="explicitly-triggering-validation"></a><span data-ttu-id="d9ac0-163">明確地觸發驗證</span><span class="sxs-lookup"><span data-stu-id="d9ac0-163">Explicitly triggering validation</span></span>

<span data-ttu-id="d9ac0-164">SaveChanges 呼叫，就會觸發所有的這篇文章中涵蓋的驗證。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-164">A call to SaveChanges triggers all of the validations covered in this article.</span></span> <span data-ttu-id="d9ac0-165">但您不需要依賴 SaveChanges。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-165">But you don’t need to rely on SaveChanges.</span></span> <span data-ttu-id="d9ac0-166">您可能會想要驗證您的應用程式中的其他位置。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-166">You may prefer to validate elsewhere in your application.</span></span>

<span data-ttu-id="d9ac0-167">DbContext.GetValidationErrors 會觸發的所有驗證、 註解或 Fluent API 所定義，在 IValidatableObject (比方說，Blog.Validate) 中建立的驗證和 DbContext.ValidateEntity 中執行的驗證方法。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-167">DbContext.GetValidationErrors will trigger all of the validations, those defined by annotations or the Fluent API, the validation created in IValidatableObject (for example, Blog.Validate), and the validations performed in the DbContext.ValidateEntity method.</span></span>

<span data-ttu-id="d9ac0-168">下列程式碼會呼叫 GetValidationErrors DbContext 的目前執行個體上。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-168">The following code will call GetValidationErrors on the current instance of a DbContext.</span></span> <span data-ttu-id="d9ac0-169">ValidationErrors 會依 DbValidationRestuls 到的實體類型。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-169">ValidationErrors are grouped by entity type into DbValidationRestuls.</span></span> <span data-ttu-id="d9ac0-170">程式碼會逐一查看第一次，由方法傳回 DbValidationResults，然後再透過每個 ValidationError 內。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-170">The code iterates first through the DbValidationResults returned by the method and then through each ValidationError inside.</span></span>

``` csharp
    foreach (var validationResults in db.GetValidationErrors())
        {
            foreach (var error in validationResults.ValidationErrors)
            {
                Debug.WriteLine(
                                  "Entity Property: {0}, Error {1}",
                                  error.PropertyName,
                                  error.ErrorMessage);
            }
        }
```

## <a name="other-considerations-when-using-validation"></a><span data-ttu-id="d9ac0-171">使用驗證時的其他考量</span><span class="sxs-lookup"><span data-stu-id="d9ac0-171">Other considerations when using validation</span></span>

<span data-ttu-id="d9ac0-172">以下是使用 Entity Framework 驗證時要考慮的其他幾個事項：</span><span class="sxs-lookup"><span data-stu-id="d9ac0-172">Here are a few other points to consider when using Entity Framework validation:</span></span>

-   <span data-ttu-id="d9ac0-173">在驗證期間停用消極式載入。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-173">Lazy loading is disabled during validation.</span></span>
-   <span data-ttu-id="d9ac0-174">EF 會驗證非對應的屬性 （未對應至資料庫中的資料行的屬性） 上的資料註解。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-174">EF will validate data annotations on non-mapped properties (properties that are not mapped to a column in the database).</span></span>
-   <span data-ttu-id="d9ac0-175">SaveChanges 期間偵測到的變更之後，會執行驗證。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-175">Validation is performed after changes are detected during SaveChanges.</span></span> <span data-ttu-id="d9ac0-176">若要在驗證期間變更您必須負責通知變更追蹤程式。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-176">If you make changes during validation it is your responsibility to notify the change tracker.</span></span>
-   <span data-ttu-id="d9ac0-177">如果在驗證期間發生錯誤，則會擲回 DbUnexpectedValidationException。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-177">DbUnexpectedValidationException is thrown if errors occur during validation.</span></span>
-   <span data-ttu-id="d9ac0-178">Entity Framework 包含模型 （所需的最大長度等） 中的 facet 將會導致驗證，即使不在類別上的資料註解及/或您使用 EF 設計工具來建立您的模型。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-178">Facets that Entity Framework includes in the model (maximum length, required, etc.) will cause validation, even if there are not data annotations on your classes and/or you used the EF Designer to create your model.</span></span>
-   <span data-ttu-id="d9ac0-179">優先順序規則：</span><span class="sxs-lookup"><span data-stu-id="d9ac0-179">Precedence rules:</span></span>
    -   <span data-ttu-id="d9ac0-180">Fluent API 呼叫覆寫對應的資料註解</span><span class="sxs-lookup"><span data-stu-id="d9ac0-180">Fluent API calls override the corresponding data annotations</span></span>
-   <span data-ttu-id="d9ac0-181">執行順序：</span><span class="sxs-lookup"><span data-stu-id="d9ac0-181">Execution order:</span></span>
    -   <span data-ttu-id="d9ac0-182">型別驗證之前發生的屬性驗證</span><span class="sxs-lookup"><span data-stu-id="d9ac0-182">Property validation occurs before type validation</span></span>
    -   <span data-ttu-id="d9ac0-183">如果屬性驗證成功，才會發生型別驗證</span><span class="sxs-lookup"><span data-stu-id="d9ac0-183">Type validation only occurs if property validation succeeds</span></span>
-   <span data-ttu-id="d9ac0-184">如果屬性是複雜也會包含其驗證：</span><span class="sxs-lookup"><span data-stu-id="d9ac0-184">If a property is complex its validation will also include:</span></span>
    -   <span data-ttu-id="d9ac0-185">複雜型別屬性的屬性層級驗證</span><span class="sxs-lookup"><span data-stu-id="d9ac0-185">Property-level validation on the complex type properties</span></span>
    -   <span data-ttu-id="d9ac0-186">在複雜型別，包括 IValidatableObject 驗證複雜類型上的型別層級驗證</span><span class="sxs-lookup"><span data-stu-id="d9ac0-186">Type level validation on the complex type, including IValidatableObject validation on the complex type</span></span>

## <a name="summary"></a><span data-ttu-id="d9ac0-187">總結</span><span class="sxs-lookup"><span data-stu-id="d9ac0-187">Summary</span></span>

<span data-ttu-id="d9ac0-188">Entity Framework 中的驗證 API 會輕易地玩在 MVC 中的用戶端驗證，但您不需要依賴用戶端驗證。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-188">The validation API in Entity Framework plays very nicely with client side validation in MVC but you don't have to rely on client-side validation.</span></span> <span data-ttu-id="d9ac0-189">Entity Framework 會負責在伺服器端驗證，DataAnnotations 或使用 code first Fluent API 已套用的設定。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-189">Entity Framework will take care of the validation on the server side for DataAnnotations or configurations you've applied with the code first Fluent API.</span></span>

<span data-ttu-id="d9ac0-190">您也看到了一些自訂行為，無論您使用 IValidatableObject 介面，或點選 DbContet.ValidateEntity 方法的擴充點。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-190">You also saw a number of extensibility points for customizing the behavior whether you use the IValidatableObject interface or tap into the DbContet.ValidateEntity method.</span></span> <span data-ttu-id="d9ac0-191">並驗證這些最後兩個方法都是透過 DbContext，不論您是使用 Code First、 Model First 或 Database First 的工作流程來描述概念模型。</span><span class="sxs-lookup"><span data-stu-id="d9ac0-191">And these last two means of validation are available through the DbContext, whether you use the Code First, Model First or Database First workflow to describe your conceptual model.</span></span>
