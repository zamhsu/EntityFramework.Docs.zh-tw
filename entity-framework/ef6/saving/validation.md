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
# <a name="data-validation"></a>資料驗證
> [!NOTE]
> **僅限 ef 4.1** ：此頁面中所討論的功能、api 等已于 Entity Framework 4.1 中引進。 如果您使用的是較舊的版本，部分或全部資訊不適用

此頁面上的內容是由 Julie Lerman () 最初撰寫的文章所調整 [https://thedatafarm.com](https://thedatafarm.com) 。

Entity Framework 提供許多驗證功能，可將其饋送至使用者介面以進行用戶端驗證，或用於伺服器端驗證。 使用 code first 時，您可以使用注釋或流暢的 API 設定來指定驗證。 您可以在程式碼中指定其他驗證和更複雜的驗證，而且不論您的模型是從 code first hails、第一個模型或第一個資料庫，都能運作。

## <a name="the-model"></a>模型

我將使用一組簡單的類別來示範驗證： Blog 和 Post。

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

Code First 使用來自元件的注釋 `System.ComponentModel.DataAnnotations` ，作為設定 code First 類別的其中一種方式。 這些批註中有提供規則 `Required` ，例如、 `MaxLength` 和 `MinLength` 。 許多 .NET 用戶端應用程式也會辨識這些批註，例如 ASP.NET MVC。 您可以使用這些批註來達成用戶端和伺服器端驗證。 例如，您可以強制 Blog Title 屬性成為必要的屬性。

``` csharp
[Required]
public string Title { get; set; }
```

在應用程式中沒有任何額外的程式碼或標記變更時，現有的 MVC 應用程式將會執行用戶端驗證，甚至使用屬性和批註名稱動態建立訊息。

![圖 1](~/ef6/media/figure01.png)

在此建立視圖的 post 方法中，Entity Framework 是用來將新的 blog 儲存至資料庫，但 MVC 的用戶端驗證會在應用程式到達該程式碼之前觸發。

用戶端驗證並不是專案符號。 使用者可能會影響瀏覽器的功能或更糟的功能，駭客可能會使用某些 trickery 來避免 UI 驗證。 但 Entity Framework 也會辨識 `Required` 注釋並進行驗證。

測試這項工作的一個簡單方式是停用 MVC 的用戶端驗證功能。 您可以在 MVC 應用程式的 web.config 檔中進行這項作業。 AppSettings 區段有 ClientValidationEnabled 的索引鍵。 將此機碼設為 false，會讓 UI 無法執行驗證。

``` xml
<appSettings>
    <add key="ClientValidationEnabled"value="false"/>
    ...
</appSettings>
```

即使已停用用戶端驗證，您仍會在應用程式中取得相同的回應。 錯誤訊息「需要標題欄位」會顯示為先前。 但現在會是伺服器端驗證的結果。 Entity Framework 會先在批註 (上執行驗證， `Required` 再 bothers 建立 `INSERT` 傳送至) 資料庫的命令，並將錯誤傳回至將顯示訊息的 MVC。

## <a name="fluent-api"></a>Fluent API

您可以使用 code first 的流暢 API （而非注釋）來取得相同的用戶端 & 伺服器端驗證。 `Required`我會使用 MaxLength 驗證來說明這一點，而不是使用。

在程式碼第一次從類別建立模型時，會套用流暢的 API 設定。 您可以藉由覆寫 DbCoNtext 類別的 OnModelCreating 方法來插入設定。 以下是一種設定，指定 BloggerName 屬性不能超過10個字元。

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

根據流暢的 API 設定所擲回的驗證錯誤不會自動到達 UI，但您可以在程式碼中加以捕捉，然後據以回應。

以下是應用程式的 BlogController 類別中的一些例外狀況處理錯誤碼，當 Entity Framework 嘗試使用超過10個字元的 BloggerName 來儲存 blog 時，會捕捉該驗證錯誤。

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

驗證不會自動傳回給視圖，這也是使用中其他程式碼的原因 `ModelState.AddModelError` 。 這可確保錯誤詳細資料會將它變成視圖，然後使用 `ValidationMessageFor` Htmlhelper 來顯示錯誤。

``` csharp
@Html.ValidationMessageFor(model => model.BloggerName)
```

## <a name="ivalidatableobject"></a>IValidatableObject

`IValidatableObject` 是存在的介面 `System.ComponentModel.DataAnnotations` 。 雖然它不是 Entity Framework API 的一部分，但是您仍然可以在 Entity Framework 類別中利用它進行伺服器端驗證。 `IValidatableObject` 提供 `Validate` Entity Framework 在 SaveChanges 期間呼叫的方法，或您可以在每次想要驗證類別時呼叫。

`Required`的設定，以及 `MaxLength` 在單一欄位上執行驗證。 在方法中， `Validate` 您可以有更複雜的邏輯，例如，比較兩個欄位。

在下列範例中， `Blog` 類別已擴充為可執行， `IValidatableObject` 然後提供 `Title` `BloggerName` 不符合的規則。

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

此函式 `ValidationResult` 會採用 `string` 代表錯誤訊息的，以及表示 `string` 與驗證相關聯之成員名稱的陣列。 由於這項驗證會同時檢查 `Title` 和 `BloggerName` ，因此會傳回兩個屬性名稱。

不同于流暢 API 所提供的驗證，此驗證結果將會由視圖辨識，而且我稍早用來將錯誤新增至的例外狀況處理常式 `ModelState` 是不必要的。 因為我在中同時設定了這兩個屬性名稱 `ValidationResult` ，所以 MVC HtmlHelpers 會顯示這兩個屬性的錯誤訊息。

![圖 2](~/ef6/media/figure02.png)

## <a name="dbcontextvalidateentity"></a>DbCoNtext. ValidateEntity

`DbContext` 具有稱為的可覆寫方法 `ValidateEntity` 。 當您呼叫時 `SaveChanges` ，Entity Framework 會針對其狀態為非的快取中的每個實體呼叫這個方法 `Unchanged` 。 您可以直接在這裡放置驗證邏輯，也可以使用這個方法來呼叫，例如，在 `Blog.Validate` 上一節中加入的方法。

以下是 `ValidateEntity` 會驗證新的覆寫， `Post` 以確保貼文標題尚未使用的覆寫範例。 它會先檢查實體是否為 post，以及其狀態是否已加入。 如果是這種情況，則會在資料庫中查看是否已有相同標題的貼文。 如果已有現有的貼文，則 `DbEntityValidationResult` 會建立新的。

`DbEntityValidationResult` 裝載 `DbEntityEntry` `ICollection<DbValidationErrors>` 單一實體的和。 在這個方法的開頭，會具 `DbEntityValidationResult` 現化，然後將探索到的任何錯誤加入其 `ValidationErrors` 集合中。

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

的呼叫會 `SaveChanges` 觸發本文涵蓋的所有驗證。 但是您不需要依賴 `SaveChanges` 。 您可能會想要在應用程式中的其他地方進行驗證。

`DbContext.GetValidationErrors` 將會觸發所有驗證（由批註或流暢的 API 定義）、在 (中建立的驗證（ `IValidatableObject` 例如 `Blog.Validate`) ），以及在方法中執行的驗證 `DbContext.ValidateEntity` 。

下列程式碼會 `GetValidationErrors` 在目前的實例上呼叫 `DbContext` 。 `ValidationErrors` 會依實體類型分組至 `DbEntityValidationResult` 。 程式碼會先逐一查看 `DbEntityValidationResult` 方法所傳回的，然後再逐一查看 `DbValidationError` 。

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

以下是使用 Entity Framework 驗證時要考慮的一些其他幾點：

- 在驗證期間停用延遲載入
- EF 會驗證非對應屬性的資料批註 (未對應到資料庫中資料行的屬性) 
- 在期間偵測到變更之後，就會執行驗證 `SaveChanges` 。 如果您在驗證期間進行變更，您必須負責通知變更追蹤器
- `DbUnexpectedValidationException` 如果驗證期間發生錯誤，則會擲回
- Entity Framework 包含在模型中的 facet (最大長度、必要項等等。即使您的類別和/或您使用 EF 設計工具建立模型，) 也會導致驗證
- 優先順序規則：
  - 流暢的 API 呼叫會覆寫對應的資料批註
- 執行順序：
  - 屬性驗證會在類型驗證之前發生
  - 只有在屬性驗證成功時才會進行類型驗證
- 如果屬性很複雜，其驗證也將包含：
  - 複雜型別屬性上的屬性層級驗證
  - 複雜型別的型別層級驗證，包括 `IValidatableObject` 複雜型別上的驗證

## <a name="summary"></a>摘要

Entity Framework 中的驗證 API 與 MVC 中的用戶端驗證非常完美，但您不需要依賴用戶端驗證。 Entity Framework 將會處理伺服器端的驗證，以取得您已使用 code first 流暢 API 套用的 DataAnnotations 或設定。

您也看到許多自訂行為的擴充點，可用於自訂您是否使用 `IValidatableObject` 介面或利用 `DbContext.ValidateEntity` 方法。 `DbContext`無論您使用 Code First、Model First 或 Database First 工作流程來描述您的概念模型，都可以透過來使用最後兩種驗證方式。
