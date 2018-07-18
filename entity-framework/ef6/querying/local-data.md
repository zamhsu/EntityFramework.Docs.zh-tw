---
title: 本機資料-EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 2eda668b-1e5d-487d-9a8c-0e3beef03fcb
caps.latest.revision: 3
ms.openlocfilehash: 79f0d2175199780d41b43088832bab808ab2fff0
ms.sourcegitcommit: f05e7b62584cf228f17390bb086a61d505712e1b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2018
ms.locfileid: "39120338"
---
# <a name="local-data"></a><span data-ttu-id="f6e81-102">本機資料</span><span class="sxs-lookup"><span data-stu-id="f6e81-102">Local Data</span></span>
<span data-ttu-id="f6e81-103">直接對 DbSet 執行 LINQ 查詢仍會一律會傳送至資料庫的查詢，但您可以存取目前記憶體中使用 DbSet.Local 屬性的資料。</span><span class="sxs-lookup"><span data-stu-id="f6e81-103">Running a LINQ query directly against a DbSet will always send a query to the database, but you can access the data that is currently in-memory using the DbSet.Local property.</span></span> <span data-ttu-id="f6e81-104">您也可以存取您使用 DbContext.Entry 和 DbContext.ChangeTracker.Entries 方法的實體有關的 EF 正在追蹤的額外資訊。</span><span class="sxs-lookup"><span data-stu-id="f6e81-104">You can also access the extra information EF is tracking about your entities using the DbContext.Entry and DbContext.ChangeTracker.Entries methods.</span></span> <span data-ttu-id="f6e81-105">本主題所示範的技巧同樣適用於使用 Code First 和 EF 設計工具所建立的模型。</span><span class="sxs-lookup"><span data-stu-id="f6e81-105">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

## <a name="using-local-to-look-at-local-data"></a><span data-ttu-id="f6e81-106">使用本機查看本機資料</span><span class="sxs-lookup"><span data-stu-id="f6e81-106">Using Local to look at local data</span></span>  

<span data-ttu-id="f6e81-107">[本機] 屬性的 DbSet 提供簡單的存取權的實體集目前正在追蹤的內容，且尚未標示為已刪除。</span><span class="sxs-lookup"><span data-stu-id="f6e81-107">The Local property of DbSet provides simple access to the entities of the set that are currently being tracked by the context and have not been marked as Deleted.</span></span> <span data-ttu-id="f6e81-108">存取 [本機] 屬性時，永遠不會使查詢傳送至資料庫。</span><span class="sxs-lookup"><span data-stu-id="f6e81-108">Accessing the Local property never causes a query to be sent to the database.</span></span> <span data-ttu-id="f6e81-109">這表示，它通常用於查詢已執行之後。</span><span class="sxs-lookup"><span data-stu-id="f6e81-109">This means that it is usually used after a query has already been performed.</span></span> <span data-ttu-id="f6e81-110">Load 擴充方法可以用來執行查詢，以便將內容追蹤結果中。</span><span class="sxs-lookup"><span data-stu-id="f6e81-110">The Load extension method can be used to execute a query so that the context tracks the results.</span></span> <span data-ttu-id="f6e81-111">例如: </span><span class="sxs-lookup"><span data-stu-id="f6e81-111">For example:</span></span>  

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

<span data-ttu-id="f6e81-112">如果我們有兩個部落格-'ADO.NET 部落格' 使用的 1 BlogId-和 'Visual Studio 部落格' 與 BlogId 2 的資料庫中，我們可以預期下列輸出：</span><span class="sxs-lookup"><span data-stu-id="f6e81-112">If we had two blogs in the database - 'ADO.NET Blog' with a BlogId of 1 and 'The Visual Studio Blog' with a BlogId of 2 - we could expect the following output:</span></span>  

```  
In Local:
Found 0: My New Blog with state Added
Found 2: The Visual Studio Blog with state Unchanged

In DbSet query:
Found 1: ADO.NET Blog with state Deleted
Found 2: The Visual Studio Blog with state Unchanged
```  

<span data-ttu-id="f6e81-113">這說明三個點：</span><span class="sxs-lookup"><span data-stu-id="f6e81-113">This illustrates three points:</span></span>  

- <span data-ttu-id="f6e81-114">新的部落格 'My 新部落格' 包含在本機集合中，即使它有尚未儲存到資料庫。</span><span class="sxs-lookup"><span data-stu-id="f6e81-114">The new blog 'My New Blog' is included in the Local collection even though it has not yet been saved to the database.</span></span> <span data-ttu-id="f6e81-115">這篇部落格有零的主索引鍵，因為資料庫尚未產生實體的實際金鑰。</span><span class="sxs-lookup"><span data-stu-id="f6e81-115">This blog has a primary key of zero because the database has not yet generated a real key for the entity.</span></span>  
- <span data-ttu-id="f6e81-116">ADO.NET 部落格內容不會包含在本機收集，即使仍由內容所追蹤。</span><span class="sxs-lookup"><span data-stu-id="f6e81-116">The 'ADO.NET Blog' is not included in the local collection even though it is still being tracked by the context.</span></span> <span data-ttu-id="f6e81-117">這是因為我們從藉此標示為已刪除的 DbSet 中移除它。</span><span class="sxs-lookup"><span data-stu-id="f6e81-117">This is because we removed it from the DbSet thereby marking it as deleted.</span></span>  
- <span data-ttu-id="f6e81-118">DbSet 來執行查詢時標示為刪除 （ADO.NET 部落格） 部落格包含在結果中，新的部落格 （我新部落格） 尚未儲存到資料庫不包含在結果中。</span><span class="sxs-lookup"><span data-stu-id="f6e81-118">When DbSet is used to perform a query the blog marked for deletion (ADO.NET Blog) is included in the results and the new blog (My New Blog) that has not yet been saved to the database is not included in the results.</span></span> <span data-ttu-id="f6e81-119">這是因為 DbSet 正在執行資料庫的查詢，而且一律傳回的結果會反映功能的資料庫。</span><span class="sxs-lookup"><span data-stu-id="f6e81-119">This is because DbSet is performing a query against the database and the results returned always reflect what is in the database.</span></span>  

## <a name="using-local-to-add-and-remove-entities-from-the-context"></a><span data-ttu-id="f6e81-120">若要新增和移除內容的實體使用本機</span><span class="sxs-lookup"><span data-stu-id="f6e81-120">Using Local to add and remove entities from the context</span></span>  

<span data-ttu-id="f6e81-121">DbSet 上的 [本機] 屬性會傳回[ObservableCollection](https://msdn.microsoft.com/library/ms668604.aspx)與連結，它會保持與內容的內容保持同步的事件。</span><span class="sxs-lookup"><span data-stu-id="f6e81-121">The Local property on DbSet returns an [ObservableCollection](https://msdn.microsoft.com/library/ms668604.aspx) with events hooked up such that it stays in sync with the contents of the context.</span></span> <span data-ttu-id="f6e81-122">這表示可以加入或移除從本機集合或 DbSet 實體。</span><span class="sxs-lookup"><span data-stu-id="f6e81-122">This means that entities can be added or removed from either the Local collection or the DbSet.</span></span> <span data-ttu-id="f6e81-123">這也表示帶入內容中的新實體的查詢會導致本機更新這些實體的集合。</span><span class="sxs-lookup"><span data-stu-id="f6e81-123">It also means that queries that bring new entities into the context will result in the Local collection being updated with those entities.</span></span> <span data-ttu-id="f6e81-124">例如: </span><span class="sxs-lookup"><span data-stu-id="f6e81-124">For example:</span></span>  

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

<span data-ttu-id="f6e81-125">假設我們有幾個使用 ' entity framework' 和 'asp.net' 輸出已標記的文章，可能看起來像這樣：</span><span class="sxs-lookup"><span data-stu-id="f6e81-125">Assuming we had a few posts tagged with 'entity-framework' and 'asp.net' the output may look something like this:</span></span>  

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

<span data-ttu-id="f6e81-126">這說明三個點：</span><span class="sxs-lookup"><span data-stu-id="f6e81-126">This illustrates three points:</span></span>  

- <span data-ttu-id="f6e81-127">新文章 'What's New in EF' 已加入至本機集合會成為 Added 狀態中的內容所追蹤。</span><span class="sxs-lookup"><span data-stu-id="f6e81-127">The new post 'What's New in EF' that was added to the Local collection becomes tracked by the context in the Added state.</span></span> <span data-ttu-id="f6e81-128">它將會因此插入資料庫讓呼叫 SaveChanges 時。</span><span class="sxs-lookup"><span data-stu-id="f6e81-128">It will therefore be inserted into the database when SaveChanges is called.</span></span>  
- <span data-ttu-id="f6e81-129">已移除本機集合 （EF 初級開發人員指南） 中的文章時，現在會標記為刪除的內容中。</span><span class="sxs-lookup"><span data-stu-id="f6e81-129">The post that was removed from the Local collection (EF Beginners Guide) is now marked as deleted in the context.</span></span> <span data-ttu-id="f6e81-130">當呼叫 SaveChanges 時，它因此會從資料庫刪除。</span><span class="sxs-lookup"><span data-stu-id="f6e81-130">It will therefore be deleted from the database when SaveChanges is called.</span></span>  
- <span data-ttu-id="f6e81-131">將具有第二個查詢的內容載入的其他文章 （ASP.NET 初級開發人員指南） 會自動加入至本機集合。</span><span class="sxs-lookup"><span data-stu-id="f6e81-131">The additional post (ASP.NET Beginners Guide) loaded into the context with the second query is automatically added to the Local collection.</span></span>  

<span data-ttu-id="f6e81-132">關於本機，請注意一個最後一件事是，因為它是 ObservableCollection 效能不是適合大量的實體。</span><span class="sxs-lookup"><span data-stu-id="f6e81-132">One final thing to note about Local is that because it is an ObservableCollection performance is not great for large numbers of entities.</span></span> <span data-ttu-id="f6e81-133">因此如果您正在處理上千個實體，在您的內容中它可能不會建議您將使用的本機。</span><span class="sxs-lookup"><span data-stu-id="f6e81-133">Therefore if you are dealing with thousands of entities in your context it may not be advisable to use Local.</span></span>  

## <a name="using-local-for-wpf-data-binding"></a><span data-ttu-id="f6e81-134">使用 WPF 資料繫結的本機</span><span class="sxs-lookup"><span data-stu-id="f6e81-134">Using Local for WPF data binding</span></span>  

<span data-ttu-id="f6e81-135">可以直接針對 WPF 應用程式中的資料繫結使用 DbSet 上的 [本機] 屬性，因為它是 ObservableCollection 的執行個體。</span><span class="sxs-lookup"><span data-stu-id="f6e81-135">The Local property on DbSet can be used directly for data binding in a WPF application because it is an instance of ObservableCollection.</span></span> <span data-ttu-id="f6e81-136">這表示它會自動前面幾節中所述與內容的內容保持同步，且內容的內容都會自動與其同步。</span><span class="sxs-lookup"><span data-stu-id="f6e81-136">As described in the previous sections this means that it will automatically stay in sync with the contents of the context and the contents of the context will automatically stay in sync with it.</span></span> <span data-ttu-id="f6e81-137">請注意，您需要以預先填入本機集合才會將繫結至既然本地永遠不會造成資料庫查詢的任何項目資料。</span><span class="sxs-lookup"><span data-stu-id="f6e81-137">Note that you do need to pre-populate the Local collection with data for there to be anything to bind to since Local never causes a database query.</span></span>  

<span data-ttu-id="f6e81-138">這不是適當的位置，如需完整的 WPF 資料繫結範例，但索引鍵的項目：</span><span class="sxs-lookup"><span data-stu-id="f6e81-138">This is not an appropriate place for a full WPF data binding sample but the key elements are:</span></span>  

- <span data-ttu-id="f6e81-139">設定繫結來源</span><span class="sxs-lookup"><span data-stu-id="f6e81-139">Setup a binding source</span></span>  
- <span data-ttu-id="f6e81-140">將它繫結至區域屬性集的</span><span class="sxs-lookup"><span data-stu-id="f6e81-140">Bind it to the Local property of your set</span></span>  
- <span data-ttu-id="f6e81-141">填入本機資料庫查詢。</span><span class="sxs-lookup"><span data-stu-id="f6e81-141">Populate Local using a query to the database.</span></span>  

## <a name="wpf-binding-to-navigation-properties"></a><span data-ttu-id="f6e81-142">導覽屬性的 WPF 繫結</span><span class="sxs-lookup"><span data-stu-id="f6e81-142">WPF binding to navigation properties</span></span>  

<span data-ttu-id="f6e81-143">如果您要進行主版/詳細資料繫結，您可能想要繫結至其中一個實體的導覽屬性的詳細資料檢視。</span><span class="sxs-lookup"><span data-stu-id="f6e81-143">If you are doing master/detail data binding you may want to bind the detail view to a navigation property of one of your entities.</span></span> <span data-ttu-id="f6e81-144">這麼做的簡單方法是使用 ObservableCollection，瀏覽屬性。</span><span class="sxs-lookup"><span data-stu-id="f6e81-144">An easy way to make this work is to use an ObservableCollection for the navigation property.</span></span> <span data-ttu-id="f6e81-145">例如: </span><span class="sxs-lookup"><span data-stu-id="f6e81-145">For example:</span></span>  

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

## <a name="using-local-to-clean-up-entities-in-savechanges"></a><span data-ttu-id="f6e81-146">使用本機 SaveChanges 中的實體進行清除</span><span class="sxs-lookup"><span data-stu-id="f6e81-146">Using Local to clean up entities in SaveChanges</span></span>  

<span data-ttu-id="f6e81-147">在大部分情況下移除導覽屬性的實體不會被自動標示為已刪除的內容中。</span><span class="sxs-lookup"><span data-stu-id="f6e81-147">In most cases entities removed from a navigation property will not be automatically marked as deleted in the context.</span></span> <span data-ttu-id="f6e81-148">比方說，如果文章物件從集合中移除 Blog.Posts 的文章，然後將不會自動刪除讓呼叫 SaveChanges 時。</span><span class="sxs-lookup"><span data-stu-id="f6e81-148">For example, if you remove a Post object from the Blog.Posts collection then that post will not be automatically deleted when SaveChanges is called.</span></span> <span data-ttu-id="f6e81-149">若要刪除，可能需要尋找這些懸空的實體，並標示為刪除，然後再呼叫 SaveChanges，或覆寫的 SaveChanges 的一部分。</span><span class="sxs-lookup"><span data-stu-id="f6e81-149">If you need it to be deleted then you may need to find these dangling entities and mark them as deleted before calling SaveChanges or as part of an overridden SaveChanges.</span></span> <span data-ttu-id="f6e81-150">例如: </span><span class="sxs-lookup"><span data-stu-id="f6e81-150">For example:</span></span>  

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

<span data-ttu-id="f6e81-151">上述程式碼會使用本機收集，以尋找所有貼文及任何沒有的部落格參考為已刪除的標記。</span><span class="sxs-lookup"><span data-stu-id="f6e81-151">The code above uses the Local collection to find all posts and marks any that do not have a blog reference as deleted.</span></span> <span data-ttu-id="f6e81-152">ToList 呼叫是必要的因為移除否則修改集合正在列舉時呼叫。</span><span class="sxs-lookup"><span data-stu-id="f6e81-152">The ToList call is required because otherwise the collection will be modified by the Remove call while it is being enumerated.</span></span> <span data-ttu-id="f6e81-153">在其他大部分的情況下您可以直接針對 [本機] 屬性而不先使用 「 ToList 」 查詢。</span><span class="sxs-lookup"><span data-stu-id="f6e81-153">In most other situations you can query directly against the Local property without using ToList first.</span></span>  

## <a name="using-local-and-tobindinglist-for-windows-forms-data-binding"></a><span data-ttu-id="f6e81-154">使用本機和 ToBindingList 適用於 Windows Form 資料繫結</span><span class="sxs-lookup"><span data-stu-id="f6e81-154">Using Local and ToBindingList for Windows Forms data binding</span></span>  

<span data-ttu-id="f6e81-155">Windows Form 不支援直接使用 ObservableCollection 完整無缺資料繫結。</span><span class="sxs-lookup"><span data-stu-id="f6e81-155">Windows Forms does not support full fidelity data binding using ObservableCollection directly.</span></span> <span data-ttu-id="f6e81-156">不過，您仍然可以使用資料繫結的 DbSet 本機屬性來取得前幾節所述的所有權益。</span><span class="sxs-lookup"><span data-stu-id="f6e81-156">However, you can still use the DbSet Local property for data binding to get all the benefits described in the previous sections.</span></span> <span data-ttu-id="f6e81-157">這透過建立 ToBindingList 擴充方法來達成[IBindingList](https://msdn.microsoft.com/library/system.componentmodel.ibindinglist.aspx)本機 ObservableCollection 所支援的實作。</span><span class="sxs-lookup"><span data-stu-id="f6e81-157">This is achieved through the ToBindingList extension method which creates an [IBindingList](https://msdn.microsoft.com/library/system.componentmodel.ibindinglist.aspx) implementation backed by the Local ObservableCollection.</span></span>  

<span data-ttu-id="f6e81-158">這不是適當的位置，如需完整的 Windows Form 資料繫結範例，但索引鍵的項目：</span><span class="sxs-lookup"><span data-stu-id="f6e81-158">This is not an appropriate place for a full Windows Forms data binding sample but the key elements are:</span></span>  

- <span data-ttu-id="f6e81-159">設定物件繫結來源</span><span class="sxs-lookup"><span data-stu-id="f6e81-159">Setup an object binding source</span></span>  
- <span data-ttu-id="f6e81-160">將它繫結至使用 Local.ToBindingList() 集的區域屬性</span><span class="sxs-lookup"><span data-stu-id="f6e81-160">Bind it to the Local property of your set using Local.ToBindingList()</span></span>  
- <span data-ttu-id="f6e81-161">填入本機資料庫查詢</span><span class="sxs-lookup"><span data-stu-id="f6e81-161">Populate Local using a query to the database</span></span>  

## <a name="getting-detailed-information-about-tracked-entities"></a><span data-ttu-id="f6e81-162">取得追蹤的實體有關的詳細的資訊</span><span class="sxs-lookup"><span data-stu-id="f6e81-162">Getting detailed information about tracked entities</span></span>  

<span data-ttu-id="f6e81-163">許多在這一系列的範例使用項目方法來傳回實體的 DbEntityEntry 執行個體。</span><span class="sxs-lookup"><span data-stu-id="f6e81-163">Many of the examples in this series use the Entry method to return a DbEntityEntry instance for an entity.</span></span> <span data-ttu-id="f6e81-164">此項目物件然後當作起點來收集資訊的實體，例如其目前的狀態，以及例如明確載入相關的實體的實體上執行作業。</span><span class="sxs-lookup"><span data-stu-id="f6e81-164">This entry object then acts as the starting point for gathering information about the entity such as its current state, as well as for performing operations on the entity such as explicitly loading a related entity.</span></span>  

<span data-ttu-id="f6e81-165">項目方法會傳回多個或所有實體內容正在追蹤的 DbEntityEntry 物件。</span><span class="sxs-lookup"><span data-stu-id="f6e81-165">The Entries methods return DbEntityEntry objects for many or all entities being tracked by the context.</span></span> <span data-ttu-id="f6e81-166">這可讓您收集資訊或執行許多實體，而不是只是單一項目上的作業。</span><span class="sxs-lookup"><span data-stu-id="f6e81-166">This allows you to gather information or perform operations on many entities rather than just a single entry.</span></span> <span data-ttu-id="f6e81-167">例如: </span><span class="sxs-lookup"><span data-stu-id="f6e81-167">For example:</span></span>  

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

<span data-ttu-id="f6e81-168">您會注意到，我們引進了作者和讀取器的類別到範例-兩個類別實作 IPerson 介面。</span><span class="sxs-lookup"><span data-stu-id="f6e81-168">You'll notice we are introducing a Author and Reader class into the example - both of these classes implement the IPerson interface.</span></span>  

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

<span data-ttu-id="f6e81-169">例如，假設我們有在資料庫中的下列資料：</span><span class="sxs-lookup"><span data-stu-id="f6e81-169">Let's assume we have the following data in the database:</span></span>

<span data-ttu-id="f6e81-170">部落格與 BlogId = 1 且名稱 = ' ADO.NET 部落格</span><span class="sxs-lookup"><span data-stu-id="f6e81-170">Blog with BlogId = 1 and Name = 'ADO.NET Blog'</span></span>  
<span data-ttu-id="f6e81-171">部落格與 BlogId = 2 和名稱 = 'Visual Studio 部落格</span><span class="sxs-lookup"><span data-stu-id="f6e81-171">Blog with BlogId = 2 and Name = 'The Visual Studio Blog'</span></span>  
<span data-ttu-id="f6e81-172">部落格與 BlogId = 3 和名稱 = '.NET Framework 部落格</span><span class="sxs-lookup"><span data-stu-id="f6e81-172">Blog with BlogId = 3 and Name = '.NET Framework Blog'</span></span>  
<span data-ttu-id="f6e81-173">作者與 AuthorId = 1 且名稱 = ' Joe Bloggs'</span><span class="sxs-lookup"><span data-stu-id="f6e81-173">Author with AuthorId = 1 and Name = 'Joe Bloggs'</span></span>  
<span data-ttu-id="f6e81-174">讀取器與 ReaderId = 1 且名稱 = ' John Doe'</span><span class="sxs-lookup"><span data-stu-id="f6e81-174">Reader with ReaderId = 1 and Name = 'John Doe'</span></span>  

<span data-ttu-id="f6e81-175">執行程式碼的輸出會是：</span><span class="sxs-lookup"><span data-stu-id="f6e81-175">The output from running the code would be:</span></span>  

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

<span data-ttu-id="f6e81-176">這些範例說明數個點：</span><span class="sxs-lookup"><span data-stu-id="f6e81-176">These examples illustrate several points:</span></span>  

- <span data-ttu-id="f6e81-177">項目方法會傳回實體的項目中所有的狀態，包括已刪除。</span><span class="sxs-lookup"><span data-stu-id="f6e81-177">The Entries methods return entries for entities in all states, including Deleted.</span></span> <span data-ttu-id="f6e81-178">比較這會排除本機刪除的實體。</span><span class="sxs-lookup"><span data-stu-id="f6e81-178">Compare this to Local which excludes Deleted entities.</span></span>  
- <span data-ttu-id="f6e81-179">使用非泛型項目方法時，會傳回所有實體類型的項目。</span><span class="sxs-lookup"><span data-stu-id="f6e81-179">Entries for all entity types are returned when the non-generic Entries method is used.</span></span> <span data-ttu-id="f6e81-180">使用泛型的項目方法時之實體的泛型類型的執行個體將只會傳回項目。</span><span class="sxs-lookup"><span data-stu-id="f6e81-180">When the generic entries method is used entries are only returned for entities that are instances of the generic type.</span></span> <span data-ttu-id="f6e81-181">這是上述用來取得所有部落格上的項目。</span><span class="sxs-lookup"><span data-stu-id="f6e81-181">This was used above to get entries for all blogs.</span></span> <span data-ttu-id="f6e81-182">它也用來取得實作 IPerson 的所有實體的項目。</span><span class="sxs-lookup"><span data-stu-id="f6e81-182">It was also used to get entries for all entities that implement IPerson.</span></span> <span data-ttu-id="f6e81-183">這示範了泛型型別不必是實際的實體類型。</span><span class="sxs-lookup"><span data-stu-id="f6e81-183">This demonstrates that the generic type does not have to be an actual entity type.</span></span>  
- <span data-ttu-id="f6e81-184">LINQ 物件可用來篩選傳回的結果。</span><span class="sxs-lookup"><span data-stu-id="f6e81-184">LINQ to Objects can be used to filter the results returned.</span></span> <span data-ttu-id="f6e81-185">這是上述用來尋找任何類型的實體，只要修改它們。</span><span class="sxs-lookup"><span data-stu-id="f6e81-185">This was used above to find entities of any type as long as they are modified.</span></span>  

<span data-ttu-id="f6e81-186">請注意，DbEntityEntry 執行個體一律會包含非 null 的實體。</span><span class="sxs-lookup"><span data-stu-id="f6e81-186">Note that DbEntityEntry instances always contain a non-null Entity.</span></span> <span data-ttu-id="f6e81-187">關聯性項目和虛設常式項目不會表示為 DbEntityEntry 執行個體這樣就不需要這些篩選。</span><span class="sxs-lookup"><span data-stu-id="f6e81-187">Relationship entries and stub entries are not represented as DbEntityEntry instances so there is no need to filter for these.</span></span>
