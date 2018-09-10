---
title: 驗證-EF6
author: divega
ms.date: 2016-10-23
ms.assetid: 77d6a095-c0d0-471e-80b9-8f9aea6108b2
ms.openlocfilehash: 65639b0f91f54ee2cd1336f6b6cd4caf45ede680
ms.sourcegitcommit: 0d36e8ff0892b7f034b765b15e041f375f88579a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2018
ms.locfileid: "44251020"
---
# <a name="data-validation"></a>資料驗證
> [!NOTE]
> **EF4.1 及更新版本僅**-功能、 Api、 Entity Framework 4.1 中導入等本頁所述。 如果您使用較早版本，部分或全部的資訊不適用

此頁面上的內容是來自和文章作者： Julie Lerman 原始撰寫 ([http://thedatafarm.com](http://thedatafarm.com))。

Entity Framework 提供許多驗證功能，可以透過餵送到用戶端驗證的使用者介面，或用於伺服器端驗證。 當第一次使用的程式碼，您可以指定使用註解或 fluent API 組態的驗證。 額外的驗證，以及更複雜，可以指定在程式碼中，並可是否模型在程式碼來自第一次，第一次模型或資料庫第一次。

## <a name="the-model"></a>模型

我將示範如何使用類別的簡單組驗證： 部落格和文章。

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
## <a name="data-annotations"></a>資料註釋

首先，程式碼會使用從 System.ComponentModel.DataAnnotations 組件的註解做為設定程式碼的第一個類別的一個方法。 這些註解之間所提供的規則，例如 「 必要 」 的 MaxLength 及 MinLength。 .NET 用戶端應用程式數目也會辨識這些註解，例如，ASP.NET MVC。 您可以達到這些註解這兩個用戶端和伺服器端驗證。 例如，您可以強制的部落格 Title 屬性是必要的屬性。

``` csharp
    [Required]
    public string Title { get; set; }
```

沒有額外的程式碼或標記變更應用程式中的，現有的 MVC 應用程式會執行用戶端驗證，甚至以動態方式建置訊息使用的屬性和註釋名稱。

![圖 1](~/ef6/media/figure01.png)

貼文中傳回的這個建立檢視的方法，將新的部落格儲存至資料庫中，使用 Entity Framework，但 MVC 的用戶端驗證，就會觸發應用程式到達該程式碼之前。

用戶端驗證不過並不確實。 使用者可能會影響其瀏覽器的功能，或更糟的是，駭客可能會使用一些以前來避免 UI 驗證。 但 Entity Framework 也會識別所需的註解並加以驗證。

若要測試此簡單的方法是停用 MVC 的用戶端驗證功能。 在 MVC 應用程式的 web.config 檔案中，您可以執行這項操作。 AppSettings 區段的 ClientValidationEnabled 有索引鍵。 將這個機碼設定為 false 會阻礙 UI 執行驗證。

``` xml
    <appSettings>
        <add key="ClientValidationEnabled"value="false"/>
        ...
    </appSettings>
```

即使使用停用用戶端驗證，您會收到相同的回應，應用程式中。 將顯示錯誤訊息 「 [標題] 欄位是必要 」，為。 但現在它將會是伺服器端驗證的結果。 Entity Framework 將會執行驗證所需的註解 （之前它甚至困擾，建置並傳送至資料庫的 INSERT 命令），mvc 會顯示訊息，這會傳回錯誤。

## <a name="fluent-api"></a>Fluent API

您可以使用程式碼第一的 fluent API 而不是註解來取得相同的用戶端戶端和伺服器端驗證。 而不是必要的使用，我將告訴您此使用 MaxLength 驗證。

Fluent API 組態會套用程式碼第一次建立模型的類別。 您可以藉由覆寫 DbContext 類別的 OnModelCreating 方法插入的設定。 以下是設定，指定 BloggerName 屬性可以不超過 10 個字元。

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

驗證錯誤擲回根據 Fluent API 組態不會自動 reach UI，但您可以擷取它在程式碼，然後回應據此。

這裡的一些例外狀況處理 Entity Framework 會嘗試使用超過 10 個字元的最大 BloggerName 儲存的部落格時，會擷取該驗證錯誤的應用程式的 BlogController 類別中的錯誤程式碼。

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

驗證不會自動傳遞回這就是為什麼使用 ModelState.AddModelError 的其他程式碼正在使用的檢視。 這可確保錯誤詳細資料，對該檢視會接著用來顯示錯誤的 ValidationMessageFor Htmlhelper 進行它。

``` csharp
    @Html.ValidationMessageFor(model => model.BloggerName)
```

## <a name="ivalidatableobject"></a>IValidatableObject

IValidatableObject 是居住在 System.ComponentModel.DataAnnotations 的介面。 雖然它不是 Entity Framework API 的一部分，您仍然可以利用它的伺服器端驗證 Entity Framework 類別中。 IValidatableObject 提供 Entity Framework 會呼叫期間 SaveChanges 或您的驗證方法可以自行呼叫任何您想要驗證之類別的時間。

如需設定和 MaxLength 執行驗證的單一欄位上。 您可以驗證方法中有更複雜的邏輯，例如，比較兩個欄位。

在下列範例中，部落格類別已擴充到實作 IValidatableObject，，然後提供 標題 和 BloggerName 無法比對的規則。

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

ValidationResult 建構函式會接受字串，表示錯誤訊息和表示與驗證相關聯之成員名稱的字串陣列。 由於這項驗證會檢查在標題和 BloggerName，則會傳回兩個屬性名稱。

不同於 Fluent API 所提供的驗證，此驗證的結果也會辨識由檢視而且我稍早用來加入 ModelState 錯誤的例外狀況處理常式是不必要。 因為在 ValidationResult 中設定這兩個屬性名稱，MVC HtmlHelpers 會顯示錯誤訊息，這兩個屬性。

![圖 2](~/ef6/media/figure02.png)

## <a name="dbcontextvalidateentity"></a>DbContext.ValidateEntity

DbContext 有呼叫 ValidateEntity 覆寫的方法。 當您呼叫 SaveChanges 時，Entity Framework 會呼叫這個方法，其狀態不是不變的快取中每個實體。 您可以直接在這裡或甚至是使用中放置驗證邏輯，這個方法來呼叫，比方說，在上一節中新增 Blog.Validate 方法。

以下是驗證以確保文章標題尚未已使用的新貼文的 ValidateEntity 覆寫的範例。 它首先會檢查實體是否某篇文章和其狀態會加入。 如果是這樣，看起來在資料庫中，以查看是否有相同標題的文章。 如果已經存在的文章，則會建立新的 DbEntityValidationResult。

DbEntityValidationResult 裝載 DbEntityEntry 和 ICollection 的 DbValidationErrors 單一實體。 在開始這個方法時，DbEntityValidationResult 具現化，並再探索到的任何錯誤會加入其 ValidationErrors 集合。

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

## <a name="explicitly-triggering-validation"></a>明確地觸發驗證

SaveChanges 呼叫，就會觸發所有的這篇文章中涵蓋的驗證。 但您不需要依賴 SaveChanges。 您可能會想要驗證您的應用程式中的其他位置。

DbContext.GetValidationErrors 會觸發的所有驗證、 註解或 Fluent API 所定義，在 IValidatableObject (比方說，Blog.Validate) 中建立的驗證和 DbContext.ValidateEntity 中執行的驗證方法。

下列程式碼會呼叫 GetValidationErrors DbContext 的目前執行個體上。 ValidationErrors 會依 DbValidationRestuls 到的實體類型。 程式碼會逐一查看第一次，由方法傳回 DbValidationResults，然後再透過每個 ValidationError 內。

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

## <a name="other-considerations-when-using-validation"></a>使用驗證時的其他考量

以下是使用 Entity Framework 驗證時要考慮的其他幾個事項：

-   在驗證期間停用消極式載入。
-   EF 會驗證非對應的屬性 （未對應至資料庫中的資料行的屬性） 上的資料註解。
-   SaveChanges 期間偵測到的變更之後，會執行驗證。 若要在驗證期間變更您必須負責通知變更追蹤程式。
-   如果在驗證期間發生錯誤，則會擲回 DbUnexpectedValidationException。
-   Entity Framework 包含模型 （所需的最大長度等） 中的 facet 將會導致驗證，即使不在類別上的資料註解及/或您使用 EF 設計工具來建立您的模型。
-   優先順序規則：
    -   Fluent API 呼叫覆寫對應的資料註解
-   執行順序：
    -   型別驗證之前發生的屬性驗證
    -   如果屬性驗證成功，才會發生型別驗證
-   如果屬性是複雜也會包含其驗證：
    -   複雜型別屬性的屬性層級驗證
    -   在複雜型別，包括 IValidatableObject 驗證複雜類型上的型別層級驗證

## <a name="summary"></a>總結

Entity Framework 中的驗證 API 會輕易地玩在 MVC 中的用戶端驗證，但您不需要依賴用戶端驗證。 Entity Framework 會負責在伺服器端驗證，DataAnnotations 或使用 code first Fluent API 已套用的設定。

您也看到了一些自訂行為，無論您使用 IValidatableObject 介面，或點選 DbContet.ValidateEntity 方法的擴充點。 並驗證這些最後兩個方法都是透過 DbContext，不論您是使用 Code First、 Model First 或 Database First 的工作流程來描述概念模型。
