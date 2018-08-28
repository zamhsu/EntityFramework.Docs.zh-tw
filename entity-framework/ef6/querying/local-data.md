---
title: 本機資料-EF6
author: divega
ms.date: 2016-10-23
ms.assetid: 2eda668b-1e5d-487d-9a8c-0e3beef03fcb
ms.openlocfilehash: dac1a1de20398501c706b118443743d47970df17
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42994270"
---
# <a name="local-data"></a>本機資料
直接對 DbSet 執行 LINQ 查詢仍會一律會傳送至資料庫的查詢，但您可以存取目前記憶體中使用 DbSet.Local 屬性的資料。 您也可以存取您使用 DbContext.Entry 和 DbContext.ChangeTracker.Entries 方法的實體有關的 EF 正在追蹤的額外資訊。 本主題所示範的技巧同樣適用於使用 Code First 和 EF 設計工具所建立的模型。  

## <a name="using-local-to-look-at-local-data"></a>使用本機查看本機資料  

[本機] 屬性的 DbSet 提供簡單的存取權的實體集目前正在追蹤的內容，且尚未標示為已刪除。 存取 [本機] 屬性時，永遠不會使查詢傳送至資料庫。 這表示，它通常用於查詢已執行之後。 Load 擴充方法可以用來執行查詢，以便將內容追蹤結果中。 例如:   

``` csharp
using (var context = new BloggingContext())
{
    // Load all blogs from the database into the context
    context.Blogs.Load();

    // Add a new blog to the context
    context.Blogs.Add(new Blog { Name = "My New Blog" });

    // Mark one of the existing blogs as Deleted
    context.Blogs.Remove(context.Blogs.Find(1));

    // Loop over the blogs in the context.
    Console.WriteLine("In Local: ");
    foreach (var blog in context.Blogs.Local)
    {
        Console.WriteLine(
            "Found {0}: {1} with state {2}",
            blog.BlogId,  
            blog.Name,
            context.Entry(blog).State);
    }

    // Perform a query against the database.
    Console.WriteLine("\nIn DbSet query: ");
    foreach (var blog in context.Blogs)
    {
        Console.WriteLine(
            "Found {0}: {1} with state {2}",
            blog.BlogId,  
            blog.Name,
            context.Entry(blog).State);
    }
}
```  

如果我們有兩個部落格-'ADO.NET 部落格' 使用的 1 BlogId-和 'Visual Studio 部落格' 與 BlogId 2 的資料庫中，我們可以預期下列輸出：  

```  
In Local:
Found 0: My New Blog with state Added
Found 2: The Visual Studio Blog with state Unchanged

In DbSet query:
Found 1: ADO.NET Blog with state Deleted
Found 2: The Visual Studio Blog with state Unchanged
```  

這說明三個點：  

- 新的部落格 'My 新部落格' 包含在本機集合中，即使它有尚未儲存到資料庫。 這篇部落格有零的主索引鍵，因為資料庫尚未產生實體的實際金鑰。  
- ADO.NET 部落格內容不會包含在本機收集，即使仍由內容所追蹤。 這是因為我們從藉此標示為已刪除的 DbSet 中移除它。  
- DbSet 來執行查詢時標示為刪除 （ADO.NET 部落格） 部落格包含在結果中，新的部落格 （我新部落格） 尚未儲存到資料庫不包含在結果中。 這是因為 DbSet 正在執行資料庫的查詢，而且一律傳回的結果會反映功能的資料庫。  

## <a name="using-local-to-add-and-remove-entities-from-the-context"></a>若要新增和移除內容的實體使用本機  

DbSet 上的 [本機] 屬性會傳回[ObservableCollection](https://msdn.microsoft.com/library/ms668604.aspx)與連結，它會保持與內容的內容保持同步的事件。 這表示可以加入或移除從本機集合或 DbSet 實體。 這也表示帶入內容中的新實體的查詢會導致本機更新這些實體的集合。 例如:   

``` csharp
using (var context = new BloggingContext())
{
    // Load some posts from the database into the context
    context.Posts.Where(p => p.Tags.Contains("entity-framework").Load();  

    // Get the local collection and make some changes to it
    var localPosts = context.Posts.Local;
    localPosts.Add(new Post { Name = "What's New in EF" });
    localPosts.Remove(context.Posts.Find(1));  

    // Loop over the posts in the context.
    Console.WriteLine("In Local after entity-framework query: ");
    foreach (var post in context.Posts.Local)
    {
        Console.WriteLine(
            "Found {0}: {1} with state {2}",
            post.Id,  
            post.Title,
            context.Entry(post).State);
    }

    var post1 = context.Posts.Find(1);
    Console.WriteLine(
        "State of post 1: {0} is {1}",
        post1.Name,  
        context.Entry(post1).State);  

    // Query some more posts from the database
    context.Posts.Where(p => p.Tags.Contains("asp.net").Load();  

    // Loop over the posts in the context again.
    Console.WriteLine("\nIn Local after asp.net query: ");
    foreach (var post in context.Posts.Local)
    {
        Console.WriteLine(
            "Found {0}: {1} with state {2}",
            post.Id,  
            post.Title,
            context.Entry(post).State);
    }
}
```  

假設我們有幾個使用 ' entity framework' 和 'asp.net' 輸出已標記的文章，可能看起來像這樣：  

```  
In Local after entity-framework query:
Found 3: EF Designer Basics with state Unchanged
Found 5: EF Code First Basics with state Unchanged
Found 0: What's New in EF with state Added
State of post 1: EF Beginners Guide is Deleted

In Local after asp.net query:
Found 3: EF Designer Basics with state Unchanged
Found 5: EF Code First Basics with state Unchanged
Found 0: What's New in EF with state Added
Found 4: ASP.NET Beginners Guide with state Unchanged
```  

這說明三個點：  

- 新文章 'What's New in EF' 已加入至本機集合會成為 Added 狀態中的內容所追蹤。 它將會因此插入資料庫讓呼叫 SaveChanges 時。  
- 已移除本機集合 （EF 初級開發人員指南） 中的文章時，現在會標記為刪除的內容中。 當呼叫 SaveChanges 時，它因此會從資料庫刪除。  
- 將具有第二個查詢的內容載入的其他文章 （ASP.NET 初級開發人員指南） 會自動加入至本機集合。  

關於本機，請注意一個最後一件事是，因為它是 ObservableCollection 效能不是適合大量的實體。 因此如果您正在處理上千個實體，在您的內容中它可能不會建議您將使用的本機。  

## <a name="using-local-for-wpf-data-binding"></a>使用 WPF 資料繫結的本機  

可以直接針對 WPF 應用程式中的資料繫結使用 DbSet 上的 [本機] 屬性，因為它是 ObservableCollection 的執行個體。 這表示它會自動前面幾節中所述與內容的內容保持同步，且內容的內容都會自動與其同步。 請注意，您需要以預先填入本機集合才會將繫結至既然本地永遠不會造成資料庫查詢的任何項目資料。  

這不是適當的位置，如需完整的 WPF 資料繫結範例，但索引鍵的項目：  

- 設定繫結來源  
- 將它繫結至區域屬性集的  
- 填入本機資料庫查詢。  

## <a name="wpf-binding-to-navigation-properties"></a>導覽屬性的 WPF 繫結  

如果您要進行主版/詳細資料繫結，您可能想要繫結至其中一個實體的導覽屬性的詳細資料檢視。 這麼做的簡單方法是使用 ObservableCollection，瀏覽屬性。 例如:   

``` csharp
public class Blog
{
    private readonly ObservableCollection<Post> _posts =
        new ObservableCollection<Post>();

    public int BlogId { get; set; }
    public string Name { get; set; }

    public virtual ObservableCollection<Post> Posts
    {
        get { return _posts; }
    }
}
```  

## <a name="using-local-to-clean-up-entities-in-savechanges"></a>使用本機 SaveChanges 中的實體進行清除  

在大部分情況下移除導覽屬性的實體不會被自動標示為已刪除的內容中。 比方說，如果文章物件從集合中移除 Blog.Posts 的文章，然後將不會自動刪除讓呼叫 SaveChanges 時。 若要刪除，可能需要尋找這些懸空的實體，並標示為刪除，然後再呼叫 SaveChanges，或覆寫的 SaveChanges 的一部分。 例如:   

``` csharp
public override int SaveChanges()
{
    foreach (var post in this.Posts.Local.ToList())
    {
        if (post.Blog == null)
        {
            this.Posts.Remove(post);
        }
    }

    return base.SaveChanges();
}
```  

上述程式碼會使用本機收集，以尋找所有貼文及任何沒有的部落格參考為已刪除的標記。 ToList 呼叫是必要的因為移除否則修改集合正在列舉時呼叫。 在其他大部分的情況下您可以直接針對 [本機] 屬性而不先使用 「 ToList 」 查詢。  

## <a name="using-local-and-tobindinglist-for-windows-forms-data-binding"></a>使用本機和 ToBindingList 適用於 Windows Form 資料繫結  

Windows Form 不支援直接使用 ObservableCollection 完整無缺資料繫結。 不過，您仍然可以使用資料繫結的 DbSet 本機屬性來取得前幾節所述的所有權益。 這透過建立 ToBindingList 擴充方法來達成[IBindingList](https://msdn.microsoft.com/library/system.componentmodel.ibindinglist.aspx)本機 ObservableCollection 所支援的實作。  

這不是適當的位置，如需完整的 Windows Form 資料繫結範例，但索引鍵的項目：  

- 設定物件繫結來源  
- 將它繫結至使用 Local.ToBindingList() 集的區域屬性  
- 填入本機資料庫查詢  

## <a name="getting-detailed-information-about-tracked-entities"></a>取得追蹤的實體有關的詳細的資訊  

許多在這一系列的範例使用項目方法來傳回實體的 DbEntityEntry 執行個體。 此項目物件然後當作起點來收集資訊的實體，例如其目前的狀態，以及例如明確載入相關的實體的實體上執行作業。  

項目方法會傳回多個或所有實體內容正在追蹤的 DbEntityEntry 物件。 這可讓您收集資訊或執行許多實體，而不是只是單一項目上的作業。 例如:   

``` csharp
using (var context = new BloggingContext())
{
    // Load some entities into the context
    context.Blogs.Load();
    context.Authors.Load();
    context.Readers.Load();

    // Make some changes
    context.Blogs.Find(1).Title = "The New ADO.NET Blog";
    context.Blogs.Remove(context.Blogs.Find(2));
    context.Authors.Add(new Author { Name = "Jane Doe" });
    context.Readers.Find(1).Username = "johndoe1987";

    // Look at the state of all entities in the context
    Console.WriteLine("All tracked entities: ");
    foreach (var entry in context.ChangeTracker.Entries())
    {
        Console.WriteLine(
            "Found entity of type {0} with state {1}",
            ObjectContext.GetObjectType(entry.Entity.GetType()).Name,
            entry.State);
    }

    // Find modified entities of any type
    Console.WriteLine("\nAll modified entities: ");
    foreach (var entry in context.ChangeTracker.Entries()
                              .Where(e => e.State == EntityState.Modified))
    {
        Console.WriteLine(
            "Found entity of type {0} with state {1}",
            ObjectContext.GetObjectType(entry.Entity.GetType()).Name,
            entry.State);
    }

    // Get some information about just the tracked blogs
    Console.WriteLine("\nTracked blogs: ");
    foreach (var entry in context.ChangeTracker.Entries<Blog>())
    {
        Console.WriteLine(
            "Found Blog {0}: {1} with original Name {2}",
            entry.Entity.BlogId,  
            entry.Entity.Name,
            entry.Property(p => p.Name).OriginalValue);
    }

    // Find all people (author or reader)
    Console.WriteLine("\nPeople: ");
    foreach (var entry in context.ChangeTracker.Entries<IPerson>())
    {
        Console.WriteLine("Found Person {0}", entry.Entity.Name);
    }
}
```  

您會注意到，我們引進了作者和讀取器的類別到範例-兩個類別實作 IPerson 介面。  

``` csharp
public class Author : IPerson
{
    public int AuthorId { get; set; }
    public string Name { get; set; }
    public string Biography { get; set; }
}

public class Reader : IPerson
{
    public int ReaderId { get; set; }
    public string Name { get; set; }
    public string Username { get; set; }
}

public interface IPerson
{
    string Name { get; }
}
```  

例如，假設我們有在資料庫中的下列資料：

部落格與 BlogId = 1 且名稱 = ' ADO.NET 部落格  
部落格與 BlogId = 2 和名稱 = 'Visual Studio 部落格  
部落格與 BlogId = 3 和名稱 = '.NET Framework 部落格  
作者與 AuthorId = 1 且名稱 = ' Joe Bloggs'  
讀取器與 ReaderId = 1 且名稱 = ' John Doe'  

執行程式碼的輸出會是：  

```  
All tracked entities:
Found entity of type Blog with state Modified
Found entity of type Blog with state Deleted
Found entity of type Blog with state Unchanged
Found entity of type Author with state Unchanged
Found entity of type Author with state Added
Found entity of type Reader with state Modified

All modified entities:
Found entity of type Blog with state Modified
Found entity of type Reader with state Modified

Tracked blogs:
Found Blog 1: The New ADO.NET Blog with original Name ADO.NET Blog
Found Blog 2: The Visual Studio Blog with original Name The Visual Studio Blog
Found Blog 3: .NET Framework Blog with original Name .NET Framework Blog

People:
Found Person John Doe
Found Person Joe Bloggs
Found Person Jane Doe
```  

這些範例說明數個點：  

- 項目方法會傳回實體的項目中所有的狀態，包括已刪除。 比較這會排除本機刪除的實體。  
- 使用非泛型項目方法時，會傳回所有實體類型的項目。 使用泛型的項目方法時之實體的泛型類型的執行個體將只會傳回項目。 這是上述用來取得所有部落格上的項目。 它也用來取得實作 IPerson 的所有實體的項目。 這示範了泛型型別不必是實際的實體類型。  
- LINQ 物件可用來篩選傳回的結果。 這是上述用來尋找任何類型的實體，只要修改它們。  

請注意，DbEntityEntry 執行個體一律會包含非 null 的實體。 關聯性項目和虛設常式項目不會表示為 DbEntityEntry 執行個體這樣就不需要這些篩選。
