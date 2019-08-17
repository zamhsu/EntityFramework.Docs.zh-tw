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
# <a name="data-validation"></a>資料驗證
> [!NOTE]
> **僅限 ef 4.1 [僅限**]-此頁面中所討論的功能、api 等已在 Entity Framework 4.1 中引進。 如果您使用的是較舊的版本, 部分或全部的資訊將不適用

此頁面上的內容可從原本由 Julie Lerman ([http://thedatafarm.com](http://thedatafarm.com)) 撰寫的文章進行調整。

Entity Framework 提供了絕佳的驗證功能, 可將其饋送至使用者介面以進行用戶端驗證, 或用於伺服器端驗證。 使用 code first 時, 您可以使用 annotation 或 Fluent API 設定來指定驗證。 您可以在程式碼中指定額外的驗證和更複雜的, 而且不論您的模型是從 code first、model first 或 database first hails, 都能正常執行。

## <a name="the-model"></a>模型

我將使用一組簡單的類別來示範驗證:Blog 和 Post。

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

## <a name="data-annotations"></a>資料註釋

Code First 使用來自`System.ComponentModel.DataAnnotations`元件的注釋, 做為設定 code First 類別的其中一種方式。 在這些批註中`Required`, 會提供、 `MaxLength`和`MinLength`等規則。 許多 .NET 用戶端應用程式也會辨識這些批註, 例如 ASP.NET MVC。 您可以使用這些批註來完成用戶端和伺服器端驗證。 例如, 您可以強制 [Blog 標題] 屬性成為必要的屬性。

``` csharp
[Required]
public string Title { get; set; }
```

在應用程式中不會有任何額外的程式碼或標記變更, 現有 MVC 應用程式會執行用戶端驗證, 甚至使用屬性和批註名稱動態建立訊息。

![圖1](~/ef6/media/figure01.png)

在此 Create view 的回傳方法中, Entity Framework 是用來將新的 blog 儲存到資料庫中, 但是在應用程式達到該程式碼之前, 會觸發 MVC 的用戶端驗證。

不過, 用戶端驗證並不是專案符號的證明。 使用者可能會影響其瀏覽器的功能或更糟的部分, 駭客可能會使用某些 trickery 來避免 UI 驗證。 但是 Entity Framework 也會辨識`Required`注釋並加以驗證。

測試此程式的簡單方法是停用 MVC 的用戶端驗證功能。 您可以在 MVC 應用程式的 web.config 檔案中執行此動作。 AppSettings 區段具有 ClientValidationEnabled 的索引鍵。 將此機碼設為 false, 將會導致 UI 無法執行驗證。

``` xml
<appSettings>
    <add key="ClientValidationEnabled"value="false"/>
    ...
</appSettings>
```

即使已停用用戶端驗證, 您仍會在應用程式中得到相同的回應。 錯誤訊息「需要標題欄位」將會顯示為「必要」。 但現在它會是伺服器端驗證的結果。 Entity Framework 會在`Required`批註上執行驗證 (甚至是 bothers 來`INSERT`建立要傳送至資料庫的命令), 並將錯誤傳回至將顯示訊息的 MVC。

## <a name="fluent-api"></a>Fluent API

您可以使用 code first 的 Fluent API 來取代注釋, 以取得相同的用戶端 & 伺服器端驗證。 我會使用`Required`MaxLength 驗證來說明這一點, 而不是使用。

在程式碼第一次從類別建立模型時, 會套用流暢的 API 設定。 您可以藉由覆寫 DbCoNtext 類別的 OnModelCreating 方法來插入設定。 以下是指定 BloggerName 屬性長度不能超過10個字元的設定。

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

以流暢的 API 設定擲回的驗證錯誤不會自動到達 UI, 但是您可以在程式碼中加以捕捉, 然後據以回應。

以下是在應用程式的 BlogController 類別中, 當 Entity Framework 嘗試使用超過10個字元上限的 BloggerName 來儲存 blog 時, 會發生該驗證錯誤的部分例外狀況處理錯誤碼。

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

驗證不會自動傳回給視圖, 這就是使用使用`ModelState.AddModelError`的其他程式碼的原因。 這可確保錯誤詳細資料會使其成為視圖, 然後使用`ValidationMessageFor` Htmlhelper 來顯示錯誤。

``` csharp
@Html.ValidationMessageFor(model => model.BloggerName)
```

## <a name="ivalidatableobject"></a>IValidatableObject

`IValidatableObject`是存在的介面`System.ComponentModel.DataAnnotations`。 雖然它不是 Entity Framework API 的一部分, 但您仍然可以將它運用在 Entity Framework 類別中的伺服器端驗證。 `IValidatableObject`提供一`Validate`種方法, Entity Framework 會在 SaveChanges 期間呼叫, 或您可以在任何時候想要驗證類別時呼叫。

`Required` 之類`MaxLength`的設定會在單一欄位上執行驗證。 `Validate`在方法中, 您甚至可以擁有更複雜的邏輯, 例如比較兩個欄位。

在`Blog`下列範例中, 類別已擴充為可執行`IValidatableObject` , 然後`Title`提供符合和`BloggerName`無法比對的規則。

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

此`ValidationResult`函式`string`會採用代表錯誤`string`訊息的, 以及代表與驗證相關聯之成員名稱的陣列。 因為這項驗證會同時`Title`檢查`BloggerName`和, 所以會傳回這兩個屬性名稱。

不同于流暢 API 所提供的驗證, 此驗證結果會由視圖辨識, 而我先前用來將錯誤新增至`ModelState`的例外狀況處理常式是不必要的。 因為我在中設定了`ValidationResult`這兩個屬性名稱, 所以 MVC HtmlHelpers 會顯示這兩個屬性的錯誤訊息。

![圖2](~/ef6/media/figure02.png)

## <a name="dbcontextvalidateentity"></a>DbContext.ValidateEntity

`DbContext`具有名`ValidateEntity`為的可覆寫方法。 當您呼叫`SaveChanges`時, Entity Framework 會針對其狀態不`Unchanged`是的快取中的每個實體呼叫此方法。 您可以將驗證邏輯直接放在這裡, 或甚至使用這個方法來呼叫, 例如上`Blog.Validate`一節中加入的方法。

以下是一個`ValidateEntity`覆寫的範例, 它會`Post`驗證新的, 以確保貼文標題尚未使用。 它會先檢查實體是否為 post, 並加入其狀態。 如果是這種情況, 則會在資料庫中查看是否已有相同標題的貼文。 如果已經有現有的貼文, 則會建立`DbEntityValidationResult`新的。

`DbEntityValidationResult``ICollection<DbValidationErrors>` `DbEntityEntry`為單一實體裝載和。 在這個方法的開頭, `DbEntityValidationResult`會具現化, 然後將探索到的任何錯誤新增至其`ValidationErrors`集合。

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

## <a name="explicitly-triggering-validation"></a>明確觸發驗證

呼叫`SaveChanges`會觸發本文中涵蓋的所有驗證。 但是您不需要依賴`SaveChanges`。 您可能想要在應用程式的其他地方進行驗證。

`DbContext.GetValidationErrors`會觸發所有驗證, 由批註或流暢 API 定義的驗證、在中`IValidatableObject`建立的驗證 ( `Blog.Validate`例如), 以及在`DbContext.ValidateEntity`方法中執行的驗證。

下列程式碼將會`GetValidationErrors`在目前的實例`DbContext`上呼叫。 `ValidationErrors`依實體類型分組至`DbEntityValidationResult`。 程式碼會先逐一查看`DbEntityValidationResult`方法所傳回的, 然後再逐一查看`DbValidationError`其中的每個。

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

## <a name="other-considerations-when-using-validation"></a>使用驗證時的其他考慮

以下是使用 Entity Framework 驗證時要考慮的幾個重點:

- 在驗證期間停用消極式載入
- EF 會驗證非對應屬性 (未對應至資料庫中資料行的屬性) 上的資料批註
- 在期間`SaveChanges`偵測到變更之後, 就會執行驗證。 如果您在驗證期間進行變更, 您必須負責通知變更追蹤程式
- `DbUnexpectedValidationException`如果驗證期間發生錯誤, 則會擲回
- Entity Framework 包含在模型中的 facet (最大長度、必要等) 會導致驗證, 即使您的類別中沒有資料批註, 以及 (或) 您使用 EF 設計工具來建立模型
- 優先順序規則:
  - 流暢的 API 呼叫會覆寫對應的資料批註
- 執行順序:
  - 屬性驗證會在類型驗證之前發生
  - 只有當屬性驗證成功時, 才會進行類型驗證
- 如果屬性很複雜, 其驗證也會包含:
  - 複雜型別屬性的屬性層級驗證
  - 複雜型別上的型別層級`IValidatableObject`驗證, 包括複雜型別上的驗證

## <a name="summary"></a>總結

Entity Framework 中的驗證 API 在 MVC 中的用戶端驗證非常好用, 但是您不需要依賴用戶端驗證。 Entity Framework 會針對您使用 code first 流暢 API 套用的 DataAnnotations 或設定, 負責處理伺服器端的驗證。

您也看到許多擴充點, 可用於自訂您是否使用`IValidatableObject`介面或點擊`DbContext.ValidateEntity`方法中的行為。 而且最後兩個驗證方法是透過`DbContext`來提供, 不論您是使用 Code First、Model First 還是 Database First 工作流程來描述概念模型。
