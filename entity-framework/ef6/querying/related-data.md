---
title: 載入相關實體-EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: c8417e18-a2ee-499c-9ce9-2a48cc5b468a
caps.latest.revision: 3
ms.openlocfilehash: e7adc9aea11a7a8e9b87b4f9e9120aa7316588db
ms.sourcegitcommit: f05e7b62584cf228f17390bb086a61d505712e1b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2018
ms.locfileid: "39120342"
---
# <a name="loading-related-entities"></a>載入相關的實體
Entity Framework 支援三種方式可以載入相關的資料-積極式載入、 消極式載入和明確式載入。 本主題所示範的技巧同樣適用於使用 Code First 和 EF 設計工具所建立的模型。  

## <a name="eagerly-loading"></a>立即載入  

積極式載入是讓某一類型實體的查詢也會載入相關的實體做為查詢一部分的程序。 積極式載入之後，即可使用 Include 方法。 例如，下列查詢會載入部落格和每個部落格與相關的所有文章。  

``` csharp
using (var context = new BloggingContext())
{
    // Load all blogs and related posts
    var blogs1 = context.Blogs
                          .Include(b => b.Posts)
                          .ToList();

    // Load one blogs and its related posts
    var blog1 = context.Blogs
                        .Where(b => b.Name == "ADO.NET Blog")
                        .Include(b => b.Posts)
                        .FirstOrDefault();

    // Load all blogs and related posts  
    // using a string to specify the relationship
    var blogs2 = context.Blogs
                          .Include("Posts")
                          .ToList();

    // Load one blog and its related posts  
    // using a string to specify the relationship
    var blog2 = context.Blogs
                        .Where(b => b.Name == "ADO.NET Blog")
                        .Include("Posts")
                        .FirstOrDefault();
}
```  

請注意，包含 System.Data.Entity 命名空間中的擴充方法因此請確定您使用該命名空間。  

### <a name="eagerly-loading-multiple-levels"></a>立即載入多個層級  

它也可提早載入相關實體的多個層級。 下列查詢會示範如何執行此動作的集合和參考導覽屬性的範例。  

``` csharp
using (var context = new BloggingContext())
{
    // Load all blogs, all related posts, and all related comments
    var blogs1 = context.Blogs
                       .Include(b => b.Posts.Select(p => p.Comments))
                       .ToList();

    // Load all users their related profiles, and related avatar
    var users1 = context.Users
                        .Include(u => u.Profile.Avatar)
                        .ToList();

    // Load all blogs, all related posts, and all related comments  
    // using a string to specify the relationships
    var blogs2 = context.Blogs
                       .Include("Posts.Comments")
                       .ToList();

    // Load all users their related profiles, and related avatar  
    // using a string to specify the relationships
    var users2 = context.Users
                        .Include("Profile.Avatar")
                        .ToList();
}
```  

請注意，它不是目前無法載入相關的實體的篩選。 包含 」 會一律將所有相關的實體。  

## <a name="lazy-loading"></a>消極式載入  

消極式載入是讓實體集合會自動從資料庫載入第一次存取的實體/實體所參考的屬性程序。 使用 POCO 實體類型時，消極式載入之後，即可建立衍生的 proxy 型別的執行個體，然後覆寫虛擬屬性，以新增載入勾點。 比方說，使用下面定義的部落格實體類別時，相關的文章將會載入第一次存取文章導覽屬性時：  

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

### <a name="turn-lazy-loading-off-for-serialization"></a>開啟的消極式載入關閉序列化  

消極式載入及序列化不要混用，以及如果您不小心得到查詢您的整個資料庫，只是因為在啟用消極式載入。 大部分的序列化程式的運作方式存取類型的執行個體上的每個屬性。 屬性存取會觸發消極式載入，因此多個實體進行序列化。 在這些實體上存取屬性，並載入更多的實體。 您最好啟用消極式載入關閉之前序列化實體。 下列各節示範如何執行這項操作。  

### <a name="turning-off-lazy-loading-for-specific-navigation-properties"></a>關閉特定的導覽屬性的消極式載入  

可以讓 [文章] 屬性的非虛擬，關閉狀態文章集合的消極式載入：  

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

正在載入文章的集合，可仍透過積極式載入 (請參閱*提早載入*上方) 或 Load 方法 (請參閱*明確載入*下方)。  

### <a name="turn-off-lazy-loading-for-all-entities"></a>關閉所有實體的消極式載入  

消極式載入可關閉的內容中的所有實體的組態屬性上設定旗標。 例如:   

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext()
    {
        this.Configuration.LazyLoadingEnabled = false;
    }
}
```  

載入相關實體，可仍透過積極式載入 (請參閱*提早載入*上方) 或 Load 方法 (請參閱*明確載入*下方)。  

## <a name="explicitly-loading"></a>明確載入  

即使是使用已停用消極式載入，仍然有可能延遲載入相關的實體，但必須透過明確呼叫。 若要執行這項操作中，您可以使用 Load 方法相關的實體的項目。 例如:   

``` csharp
using (var context = new BloggingContext())
{
    var post = context.Posts.Find(2);

    // Load the blog related to a given post
    context.Entry(post).Reference(p => p.Blog).Load();

    // Load the blog related to a given post using a string  
    context.Entry(post).Reference("Blog").Load();

    var blog = context.Blogs.Find(1);

    // Load the posts related to a given blog
    context.Entry(blog).Collection(p => p.Posts).Load();

    // Load the posts related to a given blog  
    // using a string to specify the relationship
    context.Entry(blog).Collection("Posts").Load();
}
```  

請注意當實體有另一個單一實體的導覽屬性，應使用 Reference 方法。 相反地，當實體有其他實體的集合導覽屬性時，應該使用的收集方法。  

### <a name="applying-filters-when-explicitly-loading-related-entities"></a>明確載入相關的實體時，套用篩選  

查詢方法會提供載入相關的實體時，會使用 Entity Framework 的基礎查詢存取。 您接著可以使用 LINQ 將篩選套用至查詢，然後再執行 ToList、 負載等的 LINQ 擴充方法的呼叫。查詢方法可以搭配參考及集合導覽屬性，但是是最適合用於集合，它可用來載入只有部分的集合。 例如:   

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    // Load the posts with the 'entity-framework' tag related to a given blog
    context.Entry(blog)
        .Collection(b => b.Posts)
        .Query()
        .Where(p => p.Tags.Contains("entity-framework")
        .Load();

    // Load the posts with the 'entity-framework' tag related to a given blog  
    // using a string to specify the relationship  
    context.Entry(blog)
        .Collection("Posts")
        .Query()
        .Where(p => p.Tags.Contains("entity-framework")
        .Load();
}
```  

使用查詢方法時，它通常最好是關閉的導覽屬性的消極式載入。 這是因為否則整個集合可能會取得自動載入的消極式載入機制之前或之後執行已篩選的查詢。  

請注意，雖然可以做為字串，而不是 lambda 運算式中指定的關聯性，傳回的 IQueryable 不泛型字串使用時，因此任何有用的資訊可以使用它之前，通常需要轉換方法。  

## <a name="using-query-to-count-related-entities-without-loading-them"></a>使用查詢計數而不需要載入它們的相關的實體  

有時候最好知道多少實體與相關資料庫中的另一個實體而不實際會產生載入所有這些實體的成本。 查詢方法使用 LINQ 計數方法可用來執行這項操作。 例如:   

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    // Count how many posts the blog has  
    var postCount = context.Entry(blog)
                          .Collection(b => b.Posts)
                          .Query()
                          .Count();
}
```  
