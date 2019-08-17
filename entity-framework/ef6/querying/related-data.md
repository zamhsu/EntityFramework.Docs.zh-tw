---
title: 載入相關實體-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: c8417e18-a2ee-499c-9ce9-2a48cc5b468a
ms.openlocfilehash: f40034475ed6659b60ab4317605fd1d802218d69
ms.sourcegitcommit: 7b7f774a5966b20d2aed5435a672a1edbe73b6fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/17/2019
ms.locfileid: "69565306"
---
# <a name="loading-related-entities"></a>載入相關實體
Entity Framework 支援三種載入相關資料的方式: 積極式載入、延遲載入和明確載入。 本主題所示範的技巧同樣適用於使用 Code First 和 EF 設計工具所建立的模型。  

## <a name="eagerly-loading"></a>立即載入  

積極式載入是一種處理常式, 其中一種實體類型的查詢也會在查詢過程中載入相關的實體。 您可以使用 Include 方法來完成積極式載入。 例如, 下列查詢將會載入 blog 和每個 blog 的所有相關文章。  

``` csharp
using (var context = new BloggingContext())
{
    // Load all blogs and related posts
    var blogs1 = context.Blogs
                        .Include(b => b.Posts)
                        .ToList();

    // Load one blog and its related posts
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

請注意, Include 是 System.web 命名空間中的擴充方法, 因此請確定您使用的是該命名空間。  

### <a name="eagerly-loading-multiple-levels"></a>立即載入多個層級  

也可以立即載入多個相關實體層級。 下列查詢顯示如何針對集合和參考導覽屬性執行這項操作的範例。  

``` csharp
using (var context = new BloggingContext())
{
    // Load all blogs, all related posts, and all related comments
    var blogs1 = context.Blogs
                        .Include(b => b.Posts.Select(p => p.Comments))
                        .ToList();

    // Load all users, their related profiles, and related avatar
    var users1 = context.Users
                        .Include(u => u.Profile.Avatar)
                        .ToList();

    // Load all blogs, all related posts, and all related comments  
    // using a string to specify the relationships
    var blogs2 = context.Blogs
                        .Include("Posts.Comments")
                        .ToList();

    // Load all users, their related profiles, and related avatar  
    // using a string to specify the relationships
    var users2 = context.Users
                        .Include("Profile.Avatar")
                        .ToList();
}
```  

請注意, 目前無法篩選要載入哪些相關實體。 Include 一律會帶入所有相關實體。  

## <a name="lazy-loading"></a>消極式載入  

「消極式載入」是一種程式, 會在第一次存取參考實體/實體的屬性時, 自動從資料庫載入實體或實體集合。 使用 POCO 實體類型時, 可以藉由建立衍生 proxy 類型的實例, 然後覆寫虛擬屬性來新增載入攔截, 來達到消極式載入。 例如, 使用以下定義的 Blog 實體類別時, 會在第一次存取張貼導覽屬性時載入相關文章:  

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

消極式載入和序列化不會混搭, 如果您不小心, 就可以查詢整個資料庫, 因為已啟用消極式載入。 大部分的序列化程式都可以藉由存取類型實例上的每個屬性來進行。 屬性存取會觸發延遲載入, 因此會序列化更多實體。 在存取這些實體屬性時, 也會載入更多實體。 在序列化實體之前, 最好先關閉延遲載入。 下列各節將示範如何執行這項操作。  

### <a name="turning-off-lazy-loading-for-specific-navigation-properties"></a>關閉特定導覽屬性的消極式載入  

將貼文屬性設為非虛擬, 即可關閉 Post 集合的消極式載入:  

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

您仍然可以使用積極式載入 (請參閱上面的*立即載入*) 或 Load 方法來載入貼文集合 (請參閱下面的*明確載入*)。  

### <a name="turn-off-lazy-loading-for-all-entities"></a>關閉所有實體的延遲載入  

您可以在 Configuration 屬性上設定旗標, 以關閉內容中所有實體的消極式載入。 例如：  

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext()
    {
        this.Configuration.LazyLoadingEnabled = false;
    }
}
```  

您仍然可以使用積極式載入 (請參閱上面的*立即載入*) 或 Load 方法來載入相關實體 (請參閱下面的*明確載入*)。  

## <a name="explicitly-loading"></a>明確載入  

即使已停用消極式載入, 仍然可以延遲載入相關的實體, 但必須使用明確的呼叫來完成。 若要這麼做, 請在相關實體的專案上使用 Load 方法。 例如：  

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

請注意, 當實體具有另一個單一實體的導覽屬性時, 應該使用參考方法。 另一方面, 當實體具有其他實體集合的導覽屬性時, 應該使用集合方法。  

### <a name="applying-filters-when-explicitly-loading-related-entities"></a>在明確載入相關實體時套用篩選  

查詢方法可讓您存取 Entity Framework 將在載入相關實體時使用的基礎查詢。 接著, 您可以使用 LINQ 將篩選套用至查詢, 然後再透過呼叫 LINQ 擴充方法 (例如 ToList、載入等等) 來執行。查詢方法可以同時用於參考和集合導覽屬性, 但最適用于可用於只載入集合一部分的集合。 例如：  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    // Load the posts with the 'entity-framework' tag related to a given blog
    context.Entry(blog)
           .Collection(b => b.Posts)
           .Query()
           .Where(p => p.Tags.Contains("entity-framework"))
           .Load();

    // Load the posts with the 'entity-framework' tag related to a given blog  
    // using a string to specify the relationship  
    context.Entry(blog)
           .Collection("Posts")
           .Query()
           .Where(p => p.Tags.Contains("entity-framework"))
           .Load();
}
```  

使用查詢方法時, 通常最好關閉導覽屬性的消極式載入。 這是因為在執行篩選查詢之前或之後, 延遲載入機制可能會自動載入整個集合。  

請注意, 雖然關聯性可以指定為字串, 而不是 lambda 運算式, 但當使用字串時, 傳回的 IQueryable 不是泛型, 因此通常需要 Cast 方法, 才能使用它來完成任何有用的工作。  

## <a name="using-query-to-count-related-entities-without-loading-them"></a>使用查詢來計算相關實體, 而不載入它們  

有時候, 知道有多少實體與資料庫中的另一個實體相關, 並不會實際產生載入所有這些實體的成本, 這會很有説明。 具有 LINQ Count 方法的查詢方法可以用來執行此動作。 例如：  

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
