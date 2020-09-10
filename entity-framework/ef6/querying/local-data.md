---
title: 本機資料-EF6
description: Entity Framework 6 中的本機資料
author: divega
ms.date: 10/23/2016
ms.assetid: 2eda668b-1e5d-487d-9a8c-0e3beef03fcb
uid: ef6/querying/local-data
ms.openlocfilehash: f7c4c8904a2985901491e423f655d4aea79f666d
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620305"
---
# <a name="local-data"></a>本機資料
直接針對 DbSet 執行 LINQ 查詢，一律會將查詢傳送至資料庫，但您可以使用 DbSet 來存取目前記憶體內部的資料。 您也可以使用 DbCoNtext 和 DbCoNtext ChangeTracker 專案方法，存取 EF 追蹤實體的額外資訊。 本主題所示範的技巧同樣適用於使用 Code First 和 EF 設計工具所建立的模型。  

## <a name="using-local-to-look-at-local-data"></a>使用本機來查看本機資料  

DbSet 的 Local 屬性提供簡單的存取權，讓您存取目前正由內容追蹤且尚未標示為已刪除的集合實體。 存取本機屬性絕不會導致查詢傳送至資料庫。 這表示它通常是在查詢已經執行之後使用。 載入擴充方法可以用來執行查詢，以便內容追蹤結果。 例如：  

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

如果您在資料庫中有兩個 blog：「ADO.NET Blog」的 BlogId 是1，而「Visual Studio Blog」的 BlogId 是2，我們可以預期下列輸出：  

```console
In Local:
Found 0: My New Blog with state Added
Found 2: The Visual Studio Blog with state Unchanged

In DbSet query:
Found 1: ADO.NET Blog with state Deleted
Found 2: The Visual Studio Blog with state Unchanged
```  

這會說明三個重點：  

- 新的 blog 「我的新的 Blog」已包含在本機集合中，即使尚未儲存到資料庫中也一樣。 此 blog 的主鍵為零，因為資料庫尚未為實體產生真正的索引鍵。  
- 「ADO.NET Blog」不會包含在本機集合中，即使它仍由內容追蹤也一樣。 這是因為我們將它從 DbSet 中移除，因此將其標示為已刪除。  
- 當使用 DbSet 來執行查詢時，會將已標示為要刪除的 blog (ADO.NET Blog) 包含在結果中，而新的 blog (新的 blog) 尚未儲存至資料庫，則不會包含在結果中。 這是因為 DbSet 正在對資料庫執行查詢，而傳回的結果一律會反映資料庫中的內容。  

## <a name="using-local-to-add-and-remove-entities-from-the-context"></a>使用 Local 從內容新增和移除實體  

DbSet 上的區域屬性會傳回已連結事件的 [ObservableCollection](https://msdn.microsoft.com/library/ms668604.aspx) ，使其與內容的內容保持同步。 這表示可以從本機集合或 DbSet 中加入或移除實體。 這也表示，將新實體帶入內容中的查詢會導致本機集合以這些實體進行更新。 例如：  

``` csharp
using (var context = new BloggingContext())
{
    // Load some posts from the database into the context
    context.Posts.Where(p => p.Tags.Contains("entity-framework")).Load();  

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

假設我們有幾個以 ' entity framework ' 和 ' asp.net ' 標記的貼文，輸出可能看起來像這樣：  

```console
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

這會說明三個重點：  

- 新增至本機集合的新文章「EF 中的新功能」會由新增狀態中的內容所追蹤。 因此，它會在呼叫 SaveChanges 時插入資料庫。  
- 從本機集合移除的 post (EF 初學者指南) 現在會在內容中標示為已刪除。 因此在呼叫 SaveChanges 時，將會從資料庫中刪除。  
- 使用第二個查詢載入內容中的額外 post (ASP.NET 初學者指南) 會自動加入至本機集合。  

關於本機的最後一件事是，因為這是 ObservableCollection 的效能不適合大量的實體。 因此，如果您在內容中處理數千個實體，則可能不建議使用本機。  

## <a name="using-local-for-wpf-data-binding"></a>使用本機進行 WPF 資料系結  

DbSet 上的本機屬性可直接用於 WPF 應用程式中的資料系結，因為它是 ObservableCollection 的實例。 如上一節所述，這表示它會自動與內容的內容保持同步，內容的內容將會自動保持同步。 請注意，您確實需要預先填入本機集合，其中包含要系結的任何資料，因為本機永遠不會造成資料庫查詢。  

這不是完整 WPF 資料系結範例的適當位置，但主要元素如下：  

- 設定系結來源  
- 將它系結至集合的區域屬性  
- 使用資料庫的查詢來填入本機。  

## <a name="wpf-binding-to-navigation-properties"></a>WPF 系結至導覽屬性  

如果您正在執行 master/detail 資料系結，您可能會想要將詳細資料檢視系結至其中一個實體的導覽屬性。 讓這項工作變得簡單的方法，就是使用 ObservableCollection 做為導覽屬性。 例如：  

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

## <a name="using-local-to-clean-up-entities-in-savechanges"></a>使用本機在 SaveChanges 中清除實體  

在大部分情況下，從導覽屬性中移除的實體將不會自動在內容中標示為已刪除。 例如，如果您從 Blog 集合中移除 Post 物件，則在呼叫 SaveChanges 時，將不會自動刪除該 post。 如果您需要將它刪除，您可能需要在呼叫 SaveChanges 之前，或在覆寫的 SaveChanges 中找出這些實體，並將其標示為已刪除。 例如：  

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

上述程式碼會使用本機集合來尋找所有貼文，並將沒有任何 blog 參考的任何貼文標示為已刪除。 ToList 呼叫是必要的，因為在列舉時，會由移除呼叫來修改集合。 在大部分的情況下，您可以直接針對本機屬性進行查詢，而不需要先使用 ToList。  

## <a name="using-local-and-tobindinglist-for-windows-forms-data-binding"></a>使用本機和 ToBindingList 進行 Windows Forms 資料系結  

Windows Forms 不支援直接使用 ObservableCollection 進行完全精確度的資料系結。 不過，您仍然可以使用 DbSet 區域屬性進行資料系結，以取得上一節中所述的所有優點。 這是透過 ToBindingList 擴充方法來達成，該方法會建立本機 ObservableCollection 所支援的 [IBindingList](https://msdn.microsoft.com/library/system.componentmodel.ibindinglist.aspx) 執行。  

這不是完整 Windows Forms 資料系結範例的適當位置，但主要元素如下：  

- 設定物件系結來源  
- 使用 ToBindingList ( # A1 將它系結至您集合的本機屬性  
- 使用資料庫的查詢填入本機  

## <a name="getting-detailed-information-about-tracked-entities"></a>取得有關追蹤實體的詳細資訊  

本系列中的許多範例都使用 Entry 方法來傳回實體的 DbEntityEntry 實例。 然後，這個專案物件會作為收集實體相關資訊（例如其目前狀態）的起點，以及用來在實體上執行作業的起點，例如明確載入相關實體。  

這些專案方法會針對內容所追蹤的許多或所有實體傳回 DbEntityEntry 物件。 這可讓您收集資訊，或在許多實體（而不只是單一專案）上執行作業。 例如：  

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

您將會注意到，我們會在範例中引進作者和讀者類別，這兩個類別都是實 IPerson 介面。  

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

讓我們假設資料庫中有下列資料：

BlogId = 1 和 Name = ' ADO.NET Blog ' 的 blog  
BlogId = 2 且 Name = ' Visual Studio Blog ' 的 blog  
BlogId = 3 和 Name = ' .NET Framework Blog 的 blog  
作者 = 1 且 Name = ' Joe Bloggs ' 的作者  
ReaderId = 1 且 Name = ' John Doe ' 的讀取器  

執行程式碼的輸出會是：  

```console
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

這些範例說明幾個重點：  

- 專案方法會傳回所有狀態中的實體專案，包括已刪除的專案。 將此與排除已刪除實體的本機比較。  
- 使用非泛型專案方法時，會傳回所有實體類型的專案。 使用泛型專案方法時，只會針對屬於泛型型別實例的實體傳回專案。 這是用來取得所有 blog 的專案。 它也可用來取得所有執行 IPerson 之實體的專案。 這會示範泛型型別不一定是實際的實體型別。  
- LINQ to Objects 可以用來篩選傳回的結果。 這是用來尋找任何類型的實體（只要修改它們的話）。  

請注意，DbEntityEntry 實例一律包含非 null 的實體。 關聯性專案和存根專案不會表示為 DbEntityEntry 實例，因此不需要篩選這些專案。
