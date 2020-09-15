---
title: 載入相關實體-EF6
description: 在 Entity Framework 6 中載入相關實體
author: divega
ms.date: 10/23/2016
uid: ef6/querying/related-data
ms.openlocfilehash: d1bf04f9d9017291ef3f5ec0809095df856329cd
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073869"
---
# <a name="loading-related-entities"></a>載入相關實體

Entity Framework 支援三種載入相關資料的方式：積極式載入、消極式載入和明確載入。 本主題所示範的技巧同樣適用於使用 Code First 和 EF 設計工具所建立的模型。

## <a name="eagerly-loading"></a>立即載入

積極式載入是一種處理常式，其中一種實體類型的查詢也會在查詢中載入相關實體。 您可以使用 Include 方法來達成積極式載入。 例如，下列查詢會載入 blog 以及與每個 blog 相關的所有文章。

``` csharp
using (var context = new BloggingContext())
{
    // Load all blogs and related posts.
    var blogs1 = context.Blogs
                        .Include(b => b.Posts)
                        .ToList();

    // Load one blog and its related posts.
    var blog1 = context.Blogs
                       .Where(b => b.Name == "ADO.NET Blog")
                       .Include(b => b.Posts)
                       .FirstOrDefault();

    // Load all blogs and related posts
    // using a string to specify the relationship.
    var blogs2 = context.Blogs
                        .Include("Posts")
                        .ToList();

    // Load one blog and its related posts
    // using a string to specify the relationship.
    var blog2 = context.Blogs
                       .Where(b => b.Name == "ADO.NET Blog")
                       .Include("Posts")
                       .FirstOrDefault();
}
```

> [!NOTE]
> Include 是 system.string 命名空間中的擴充方法，因此請確定您使用的是該命名空間。

### <a name="eagerly-loading-multiple-levels"></a>立即載入多個層級

您也可以立即載入多個相關實體層級。 下列查詢顯示如何針對集合和參考導覽屬性進行這項作業的範例。  

``` csharp
using (var context = new BloggingContext())
{
    // Load all blogs, all related posts, and all related comments.
    var blogs1 = context.Blogs
                        .Include(b => b.Posts.Select(p => p.Comments))
                        .ToList();

    // Load all users, their related profiles, and related avatar.
    var users1 = context.Users
                        .Include(u => u.Profile.Avatar)
                        .ToList();

    // Load all blogs, all related posts, and all related comments  
    // using a string to specify the relationships.
    var blogs2 = context.Blogs
                        .Include("Posts.Comments")
                        .ToList();

    // Load all users, their related profiles, and related avatar  
    // using a string to specify the relationships.
    var users2 = context.Users
                        .Include("Profile.Avatar")
                        .ToList();
}
```  

> [!NOTE]
> 目前無法篩選已載入的相關實體。 Include 一律會帶入所有相關的實體。  

## <a name="lazy-loading"></a>消極式載入

消極式載入是一種處理常式，會在第一次存取參考實體/實體的屬性時，從資料庫自動載入實體或實體集合。 使用 POCO 實體型別時，可以藉由建立衍生 proxy 型別的實例，然後覆寫虛擬屬性來新增載入攔截，來達成消極式載入。 例如，使用以下定義的 Blog 實體類別時，將會在第一次存取 Post 導覽屬性時載入相關文章：

``` csharp
public class Blog
{
    public int BlogId { get; set; }
    public string Name { get; set; }
    public string Url { get; set; }
    public string Tags { get; set; }

    public virtual ICollection<Post> Posts { get; set; }
}
```

### <a name="turn-lazy-loading-off-for-serialization"></a>關閉延遲載入以進行序列化

消極式載入和序列化不會有很大的混淆，如果您不小心，只要啟用消極式載入，就能最後查詢整個資料庫。 大部分的序列化程式都可以藉由存取型別實例上的每個屬性來運作。 屬性存取會觸發消極式載入，因此會序列化更多實體。 系統會在這些實體屬性上存取，甚至載入更多實體。 在序列化實體之前，將消極式載入關閉是很好的做法。 下列各節將示範如何執行這項操作。

### <a name="turning-off-lazy-loading-for-specific-navigation-properties"></a>關閉特定導覽屬性的消極式載入

Post 集合的消極式載入可以藉由將貼文屬性設為非虛擬來關閉：

``` csharp
public class Blog
{
    public int BlogId { get; set; }
    public string Name { get; set; }
    public string Url { get; set; }
    public string Tags { get; set; }

    public ICollection<Post> Posts { get; set; }
}
```

您仍然可以使用積極式載入來完成貼文集合的載入 (請參閱上述的 *立即載入*) 或 Load 方法 (請參閱下面的 *明確載入*) 。

### <a name="turn-off-lazy-loading-for-all-entities"></a>關閉所有實體的延遲載入

您可以藉由設定設定屬性上的旗標，關閉內容中所有實體的消極式載入。 例如：

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext()
    {
        this.Configuration.LazyLoadingEnabled = false;
    }
}
```

您仍然可以使用積極式載入來完成相關實體的載入 (請參閱上述的 *立即載入*) 或 Load 方法 (請參閱下面的 *明確載入*) 。

## <a name="explicitly-loading"></a>明確載入

即使已停用消極式載入，仍然可以延遲載入相關實體，但必須透過明確呼叫來完成。 若要這樣做，請在相關實體的專案上使用 Load 方法。 例如：

``` csharp
using (var context = new BloggingContext())
{
    var post = context.Posts.Find(2);

    // Load the blog related to a given post.
    context.Entry(post).Reference(p => p.Blog).Load();

    // Load the blog related to a given post using a string.
    context.Entry(post).Reference("Blog").Load();

    var blog = context.Blogs.Find(1);

    // Load the posts related to a given blog.
    context.Entry(blog).Collection(p => p.Posts).Load();

    // Load the posts related to a given blog
    // using a string to specify the relationship.
    context.Entry(blog).Collection("Posts").Load();
}
```

> [!NOTE]
> 當實體有指向另一個單一實體的導覽屬性時，應該使用參考方法。 相反地，當實體有指向其他實體集合的導覽屬性時，應該使用 Collection 方法。

### <a name="applying-filters-when-explicitly-loading-related-entities"></a>明確載入相關實體時套用篩選

查詢方法可讓您存取 Entity Framework 將在載入相關實體時使用的基礎查詢。 然後，您可以使用 LINQ 將篩選套用至查詢，然後再透過呼叫 LINQ 擴充方法（例如 ToList、載入等）來執行。查詢方法可以搭配參考和集合導覽屬性使用，但對於只能用來載入部分集合的集合而言最有用。 例如：

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    // Load the posts with the 'entity-framework' tag related to a given blog.
    context.Entry(blog)
           .Collection(b => b.Posts)
           .Query()
           .Where(p => p.Tags.Contains("entity-framework"))
           .Load();

    // Load the posts with the 'entity-framework' tag related to a given blog
    // using a string to specify the relationship.
    context.Entry(blog)
           .Collection("Posts")
           .Query()
           .Where(p => p.Tags.Contains("entity-framework"))
           .Load();
}
```

使用查詢方法時，通常最好關閉導覽屬性的消極式載入。 這是因為否則，在執行篩選查詢之前或之後，可能會自動載入整個集合。

> [!NOTE]
> 雖然可以將關聯性指定為字串，而不是 lambda 運算式，但傳回的 IQueryable 在使用字串時不是泛型，因此通常需要 Cast 方法，才能使用它來完成任何有用的動作。

## <a name="using-query-to-count-related-entities-without-loading-them"></a>使用查詢來計算相關實體，但不載入它們

有時候，知道有多少個實體與資料庫中的另一個實體相關，並不會實際產生載入所有這些實體的成本，會很有用。 使用 LINQ Count 方法的查詢方法可以用來進行此作業。 例如：

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    // Count how many posts the blog has.
    var postCount = context.Entry(blog)
                           .Collection(b => b.Posts)
                           .Query()
                           .Count();
}
```
