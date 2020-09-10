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
# <a name="local-data"></a><span data-ttu-id="4c2b9-103">本機資料</span><span class="sxs-lookup"><span data-stu-id="4c2b9-103">Local Data</span></span>
<span data-ttu-id="4c2b9-104">直接針對 DbSet 執行 LINQ 查詢，一律會將查詢傳送至資料庫，但您可以使用 DbSet 來存取目前記憶體內部的資料。</span><span class="sxs-lookup"><span data-stu-id="4c2b9-104">Running a LINQ query directly against a DbSet will always send a query to the database, but you can access the data that is currently in-memory using the DbSet.Local property.</span></span> <span data-ttu-id="4c2b9-105">您也可以使用 DbCoNtext 和 DbCoNtext ChangeTracker 專案方法，存取 EF 追蹤實體的額外資訊。</span><span class="sxs-lookup"><span data-stu-id="4c2b9-105">You can also access the extra information EF is tracking about your entities using the DbContext.Entry and DbContext.ChangeTracker.Entries methods.</span></span> <span data-ttu-id="4c2b9-106">本主題所示範的技巧同樣適用於使用 Code First 和 EF 設計工具所建立的模型。</span><span class="sxs-lookup"><span data-stu-id="4c2b9-106">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

## <a name="using-local-to-look-at-local-data"></a><span data-ttu-id="4c2b9-107">使用本機來查看本機資料</span><span class="sxs-lookup"><span data-stu-id="4c2b9-107">Using Local to look at local data</span></span>  

<span data-ttu-id="4c2b9-108">DbSet 的 Local 屬性提供簡單的存取權，讓您存取目前正由內容追蹤且尚未標示為已刪除的集合實體。</span><span class="sxs-lookup"><span data-stu-id="4c2b9-108">The Local property of DbSet provides simple access to the entities of the set that are currently being tracked by the context and have not been marked as Deleted.</span></span> <span data-ttu-id="4c2b9-109">存取本機屬性絕不會導致查詢傳送至資料庫。</span><span class="sxs-lookup"><span data-stu-id="4c2b9-109">Accessing the Local property never causes a query to be sent to the database.</span></span> <span data-ttu-id="4c2b9-110">這表示它通常是在查詢已經執行之後使用。</span><span class="sxs-lookup"><span data-stu-id="4c2b9-110">This means that it is usually used after a query has already been performed.</span></span> <span data-ttu-id="4c2b9-111">載入擴充方法可以用來執行查詢，以便內容追蹤結果。</span><span class="sxs-lookup"><span data-stu-id="4c2b9-111">The Load extension method can be used to execute a query so that the context tracks the results.</span></span> <span data-ttu-id="4c2b9-112">例如：</span><span class="sxs-lookup"><span data-stu-id="4c2b9-112">For example:</span></span>  

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

<span data-ttu-id="4c2b9-113">如果您在資料庫中有兩個 blog：「ADO.NET Blog」的 BlogId 是1，而「Visual Studio Blog」的 BlogId 是2，我們可以預期下列輸出：</span><span class="sxs-lookup"><span data-stu-id="4c2b9-113">If we had two blogs in the database - 'ADO.NET Blog' with a BlogId of 1 and 'The Visual Studio Blog' with a BlogId of 2 - we could expect the following output:</span></span>  

```console
In Local:
Found 0: My New Blog with state Added
Found 2: The Visual Studio Blog with state Unchanged

In DbSet query:
Found 1: ADO.NET Blog with state Deleted
Found 2: The Visual Studio Blog with state Unchanged
```  

<span data-ttu-id="4c2b9-114">這會說明三個重點：</span><span class="sxs-lookup"><span data-stu-id="4c2b9-114">This illustrates three points:</span></span>  

- <span data-ttu-id="4c2b9-115">新的 blog 「我的新的 Blog」已包含在本機集合中，即使尚未儲存到資料庫中也一樣。</span><span class="sxs-lookup"><span data-stu-id="4c2b9-115">The new blog 'My New Blog' is included in the Local collection even though it has not yet been saved to the database.</span></span> <span data-ttu-id="4c2b9-116">此 blog 的主鍵為零，因為資料庫尚未為實體產生真正的索引鍵。</span><span class="sxs-lookup"><span data-stu-id="4c2b9-116">This blog has a primary key of zero because the database has not yet generated a real key for the entity.</span></span>  
- <span data-ttu-id="4c2b9-117">「ADO.NET Blog」不會包含在本機集合中，即使它仍由內容追蹤也一樣。</span><span class="sxs-lookup"><span data-stu-id="4c2b9-117">The 'ADO.NET Blog' is not included in the local collection even though it is still being tracked by the context.</span></span> <span data-ttu-id="4c2b9-118">這是因為我們將它從 DbSet 中移除，因此將其標示為已刪除。</span><span class="sxs-lookup"><span data-stu-id="4c2b9-118">This is because we removed it from the DbSet thereby marking it as deleted.</span></span>  
- <span data-ttu-id="4c2b9-119">當使用 DbSet 來執行查詢時，會將已標示為要刪除的 blog (ADO.NET Blog) 包含在結果中，而新的 blog (新的 blog) 尚未儲存至資料庫，則不會包含在結果中。</span><span class="sxs-lookup"><span data-stu-id="4c2b9-119">When DbSet is used to perform a query the blog marked for deletion (ADO.NET Blog) is included in the results and the new blog (My New Blog) that has not yet been saved to the database is not included in the results.</span></span> <span data-ttu-id="4c2b9-120">這是因為 DbSet 正在對資料庫執行查詢，而傳回的結果一律會反映資料庫中的內容。</span><span class="sxs-lookup"><span data-stu-id="4c2b9-120">This is because DbSet is performing a query against the database and the results returned always reflect what is in the database.</span></span>  

## <a name="using-local-to-add-and-remove-entities-from-the-context"></a><span data-ttu-id="4c2b9-121">使用 Local 從內容新增和移除實體</span><span class="sxs-lookup"><span data-stu-id="4c2b9-121">Using Local to add and remove entities from the context</span></span>  

<span data-ttu-id="4c2b9-122">DbSet 上的區域屬性會傳回已連結事件的 [ObservableCollection](https://msdn.microsoft.com/library/ms668604.aspx) ，使其與內容的內容保持同步。</span><span class="sxs-lookup"><span data-stu-id="4c2b9-122">The Local property on DbSet returns an [ObservableCollection](https://msdn.microsoft.com/library/ms668604.aspx) with events hooked up such that it stays in sync with the contents of the context.</span></span> <span data-ttu-id="4c2b9-123">這表示可以從本機集合或 DbSet 中加入或移除實體。</span><span class="sxs-lookup"><span data-stu-id="4c2b9-123">This means that entities can be added or removed from either the Local collection or the DbSet.</span></span> <span data-ttu-id="4c2b9-124">這也表示，將新實體帶入內容中的查詢會導致本機集合以這些實體進行更新。</span><span class="sxs-lookup"><span data-stu-id="4c2b9-124">It also means that queries that bring new entities into the context will result in the Local collection being updated with those entities.</span></span> <span data-ttu-id="4c2b9-125">例如：</span><span class="sxs-lookup"><span data-stu-id="4c2b9-125">For example:</span></span>  

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

<span data-ttu-id="4c2b9-126">假設我們有幾個以 ' entity framework ' 和 ' asp.net ' 標記的貼文，輸出可能看起來像這樣：</span><span class="sxs-lookup"><span data-stu-id="4c2b9-126">Assuming we had a few posts tagged with 'entity-framework' and 'asp.net' the output may look something like this:</span></span>  

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

<span data-ttu-id="4c2b9-127">這會說明三個重點：</span><span class="sxs-lookup"><span data-stu-id="4c2b9-127">This illustrates three points:</span></span>  

- <span data-ttu-id="4c2b9-128">新增至本機集合的新文章「EF 中的新功能」會由新增狀態中的內容所追蹤。</span><span class="sxs-lookup"><span data-stu-id="4c2b9-128">The new post 'What's New in EF' that was added to the Local collection becomes tracked by the context in the Added state.</span></span> <span data-ttu-id="4c2b9-129">因此，它會在呼叫 SaveChanges 時插入資料庫。</span><span class="sxs-lookup"><span data-stu-id="4c2b9-129">It will therefore be inserted into the database when SaveChanges is called.</span></span>  
- <span data-ttu-id="4c2b9-130">從本機集合移除的 post (EF 初學者指南) 現在會在內容中標示為已刪除。</span><span class="sxs-lookup"><span data-stu-id="4c2b9-130">The post that was removed from the Local collection (EF Beginners Guide) is now marked as deleted in the context.</span></span> <span data-ttu-id="4c2b9-131">因此在呼叫 SaveChanges 時，將會從資料庫中刪除。</span><span class="sxs-lookup"><span data-stu-id="4c2b9-131">It will therefore be deleted from the database when SaveChanges is called.</span></span>  
- <span data-ttu-id="4c2b9-132">使用第二個查詢載入內容中的額外 post (ASP.NET 初學者指南) 會自動加入至本機集合。</span><span class="sxs-lookup"><span data-stu-id="4c2b9-132">The additional post (ASP.NET Beginners Guide) loaded into the context with the second query is automatically added to the Local collection.</span></span>  

<span data-ttu-id="4c2b9-133">關於本機的最後一件事是，因為這是 ObservableCollection 的效能不適合大量的實體。</span><span class="sxs-lookup"><span data-stu-id="4c2b9-133">One final thing to note about Local is that because it is an ObservableCollection performance is not great for large numbers of entities.</span></span> <span data-ttu-id="4c2b9-134">因此，如果您在內容中處理數千個實體，則可能不建議使用本機。</span><span class="sxs-lookup"><span data-stu-id="4c2b9-134">Therefore if you are dealing with thousands of entities in your context it may not be advisable to use Local.</span></span>  

## <a name="using-local-for-wpf-data-binding"></a><span data-ttu-id="4c2b9-135">使用本機進行 WPF 資料系結</span><span class="sxs-lookup"><span data-stu-id="4c2b9-135">Using Local for WPF data binding</span></span>  

<span data-ttu-id="4c2b9-136">DbSet 上的本機屬性可直接用於 WPF 應用程式中的資料系結，因為它是 ObservableCollection 的實例。</span><span class="sxs-lookup"><span data-stu-id="4c2b9-136">The Local property on DbSet can be used directly for data binding in a WPF application because it is an instance of ObservableCollection.</span></span> <span data-ttu-id="4c2b9-137">如上一節所述，這表示它會自動與內容的內容保持同步，內容的內容將會自動保持同步。</span><span class="sxs-lookup"><span data-stu-id="4c2b9-137">As described in the previous sections this means that it will automatically stay in sync with the contents of the context and the contents of the context will automatically stay in sync with it.</span></span> <span data-ttu-id="4c2b9-138">請注意，您確實需要預先填入本機集合，其中包含要系結的任何資料，因為本機永遠不會造成資料庫查詢。</span><span class="sxs-lookup"><span data-stu-id="4c2b9-138">Note that you do need to pre-populate the Local collection with data for there to be anything to bind to since Local never causes a database query.</span></span>  

<span data-ttu-id="4c2b9-139">這不是完整 WPF 資料系結範例的適當位置，但主要元素如下：</span><span class="sxs-lookup"><span data-stu-id="4c2b9-139">This is not an appropriate place for a full WPF data binding sample but the key elements are:</span></span>  

- <span data-ttu-id="4c2b9-140">設定系結來源</span><span class="sxs-lookup"><span data-stu-id="4c2b9-140">Setup a binding source</span></span>  
- <span data-ttu-id="4c2b9-141">將它系結至集合的區域屬性</span><span class="sxs-lookup"><span data-stu-id="4c2b9-141">Bind it to the Local property of your set</span></span>  
- <span data-ttu-id="4c2b9-142">使用資料庫的查詢來填入本機。</span><span class="sxs-lookup"><span data-stu-id="4c2b9-142">Populate Local using a query to the database.</span></span>  

## <a name="wpf-binding-to-navigation-properties"></a><span data-ttu-id="4c2b9-143">WPF 系結至導覽屬性</span><span class="sxs-lookup"><span data-stu-id="4c2b9-143">WPF binding to navigation properties</span></span>  

<span data-ttu-id="4c2b9-144">如果您正在執行 master/detail 資料系結，您可能會想要將詳細資料檢視系結至其中一個實體的導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="4c2b9-144">If you are doing master/detail data binding you may want to bind the detail view to a navigation property of one of your entities.</span></span> <span data-ttu-id="4c2b9-145">讓這項工作變得簡單的方法，就是使用 ObservableCollection 做為導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="4c2b9-145">An easy way to make this work is to use an ObservableCollection for the navigation property.</span></span> <span data-ttu-id="4c2b9-146">例如：</span><span class="sxs-lookup"><span data-stu-id="4c2b9-146">For example:</span></span>  

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

## <a name="using-local-to-clean-up-entities-in-savechanges"></a><span data-ttu-id="4c2b9-147">使用本機在 SaveChanges 中清除實體</span><span class="sxs-lookup"><span data-stu-id="4c2b9-147">Using Local to clean up entities in SaveChanges</span></span>  

<span data-ttu-id="4c2b9-148">在大部分情況下，從導覽屬性中移除的實體將不會自動在內容中標示為已刪除。</span><span class="sxs-lookup"><span data-stu-id="4c2b9-148">In most cases entities removed from a navigation property will not be automatically marked as deleted in the context.</span></span> <span data-ttu-id="4c2b9-149">例如，如果您從 Blog 集合中移除 Post 物件，則在呼叫 SaveChanges 時，將不會自動刪除該 post。</span><span class="sxs-lookup"><span data-stu-id="4c2b9-149">For example, if you remove a Post object from the Blog.Posts collection then that post will not be automatically deleted when SaveChanges is called.</span></span> <span data-ttu-id="4c2b9-150">如果您需要將它刪除，您可能需要在呼叫 SaveChanges 之前，或在覆寫的 SaveChanges 中找出這些實體，並將其標示為已刪除。</span><span class="sxs-lookup"><span data-stu-id="4c2b9-150">If you need it to be deleted then you may need to find these dangling entities and mark them as deleted before calling SaveChanges or as part of an overridden SaveChanges.</span></span> <span data-ttu-id="4c2b9-151">例如：</span><span class="sxs-lookup"><span data-stu-id="4c2b9-151">For example:</span></span>  

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

<span data-ttu-id="4c2b9-152">上述程式碼會使用本機集合來尋找所有貼文，並將沒有任何 blog 參考的任何貼文標示為已刪除。</span><span class="sxs-lookup"><span data-stu-id="4c2b9-152">The code above uses the Local collection to find all posts and marks any that do not have a blog reference as deleted.</span></span> <span data-ttu-id="4c2b9-153">ToList 呼叫是必要的，因為在列舉時，會由移除呼叫來修改集合。</span><span class="sxs-lookup"><span data-stu-id="4c2b9-153">The ToList call is required because otherwise the collection will be modified by the Remove call while it is being enumerated.</span></span> <span data-ttu-id="4c2b9-154">在大部分的情況下，您可以直接針對本機屬性進行查詢，而不需要先使用 ToList。</span><span class="sxs-lookup"><span data-stu-id="4c2b9-154">In most other situations you can query directly against the Local property without using ToList first.</span></span>  

## <a name="using-local-and-tobindinglist-for-windows-forms-data-binding"></a><span data-ttu-id="4c2b9-155">使用本機和 ToBindingList 進行 Windows Forms 資料系結</span><span class="sxs-lookup"><span data-stu-id="4c2b9-155">Using Local and ToBindingList for Windows Forms data binding</span></span>  

<span data-ttu-id="4c2b9-156">Windows Forms 不支援直接使用 ObservableCollection 進行完全精確度的資料系結。</span><span class="sxs-lookup"><span data-stu-id="4c2b9-156">Windows Forms does not support full fidelity data binding using ObservableCollection directly.</span></span> <span data-ttu-id="4c2b9-157">不過，您仍然可以使用 DbSet 區域屬性進行資料系結，以取得上一節中所述的所有優點。</span><span class="sxs-lookup"><span data-stu-id="4c2b9-157">However, you can still use the DbSet Local property for data binding to get all the benefits described in the previous sections.</span></span> <span data-ttu-id="4c2b9-158">這是透過 ToBindingList 擴充方法來達成，該方法會建立本機 ObservableCollection 所支援的 [IBindingList](https://msdn.microsoft.com/library/system.componentmodel.ibindinglist.aspx) 執行。</span><span class="sxs-lookup"><span data-stu-id="4c2b9-158">This is achieved through the ToBindingList extension method which creates an [IBindingList](https://msdn.microsoft.com/library/system.componentmodel.ibindinglist.aspx) implementation backed by the Local ObservableCollection.</span></span>  

<span data-ttu-id="4c2b9-159">這不是完整 Windows Forms 資料系結範例的適當位置，但主要元素如下：</span><span class="sxs-lookup"><span data-stu-id="4c2b9-159">This is not an appropriate place for a full Windows Forms data binding sample but the key elements are:</span></span>  

- <span data-ttu-id="4c2b9-160">設定物件系結來源</span><span class="sxs-lookup"><span data-stu-id="4c2b9-160">Setup an object binding source</span></span>  
- <span data-ttu-id="4c2b9-161">使用 ToBindingList ( # A1 將它系結至您集合的本機屬性</span><span class="sxs-lookup"><span data-stu-id="4c2b9-161">Bind it to the Local property of your set using Local.ToBindingList()</span></span>  
- <span data-ttu-id="4c2b9-162">使用資料庫的查詢填入本機</span><span class="sxs-lookup"><span data-stu-id="4c2b9-162">Populate Local using a query to the database</span></span>  

## <a name="getting-detailed-information-about-tracked-entities"></a><span data-ttu-id="4c2b9-163">取得有關追蹤實體的詳細資訊</span><span class="sxs-lookup"><span data-stu-id="4c2b9-163">Getting detailed information about tracked entities</span></span>  

<span data-ttu-id="4c2b9-164">本系列中的許多範例都使用 Entry 方法來傳回實體的 DbEntityEntry 實例。</span><span class="sxs-lookup"><span data-stu-id="4c2b9-164">Many of the examples in this series use the Entry method to return a DbEntityEntry instance for an entity.</span></span> <span data-ttu-id="4c2b9-165">然後，這個專案物件會作為收集實體相關資訊（例如其目前狀態）的起點，以及用來在實體上執行作業的起點，例如明確載入相關實體。</span><span class="sxs-lookup"><span data-stu-id="4c2b9-165">This entry object then acts as the starting point for gathering information about the entity such as its current state, as well as for performing operations on the entity such as explicitly loading a related entity.</span></span>  

<span data-ttu-id="4c2b9-166">這些專案方法會針對內容所追蹤的許多或所有實體傳回 DbEntityEntry 物件。</span><span class="sxs-lookup"><span data-stu-id="4c2b9-166">The Entries methods return DbEntityEntry objects for many or all entities being tracked by the context.</span></span> <span data-ttu-id="4c2b9-167">這可讓您收集資訊，或在許多實體（而不只是單一專案）上執行作業。</span><span class="sxs-lookup"><span data-stu-id="4c2b9-167">This allows you to gather information or perform operations on many entities rather than just a single entry.</span></span> <span data-ttu-id="4c2b9-168">例如：</span><span class="sxs-lookup"><span data-stu-id="4c2b9-168">For example:</span></span>  

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

<span data-ttu-id="4c2b9-169">您將會注意到，我們會在範例中引進作者和讀者類別，這兩個類別都是實 IPerson 介面。</span><span class="sxs-lookup"><span data-stu-id="4c2b9-169">You'll notice we are introducing a Author and Reader class into the example - both of these classes implement the IPerson interface.</span></span>  

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

<span data-ttu-id="4c2b9-170">讓我們假設資料庫中有下列資料：</span><span class="sxs-lookup"><span data-stu-id="4c2b9-170">Let's assume we have the following data in the database:</span></span>

<span data-ttu-id="4c2b9-171">BlogId = 1 和 Name = ' ADO.NET Blog ' 的 blog</span><span class="sxs-lookup"><span data-stu-id="4c2b9-171">Blog with BlogId = 1 and Name = 'ADO.NET Blog'</span></span>  
<span data-ttu-id="4c2b9-172">BlogId = 2 且 Name = ' Visual Studio Blog ' 的 blog</span><span class="sxs-lookup"><span data-stu-id="4c2b9-172">Blog with BlogId = 2 and Name = 'The Visual Studio Blog'</span></span>  
<span data-ttu-id="4c2b9-173">BlogId = 3 和 Name = ' .NET Framework Blog 的 blog</span><span class="sxs-lookup"><span data-stu-id="4c2b9-173">Blog with BlogId = 3 and Name = '.NET Framework Blog'</span></span>  
<span data-ttu-id="4c2b9-174">作者 = 1 且 Name = ' Joe Bloggs ' 的作者</span><span class="sxs-lookup"><span data-stu-id="4c2b9-174">Author with AuthorId = 1 and Name = 'Joe Bloggs'</span></span>  
<span data-ttu-id="4c2b9-175">ReaderId = 1 且 Name = ' John Doe ' 的讀取器</span><span class="sxs-lookup"><span data-stu-id="4c2b9-175">Reader with ReaderId = 1 and Name = 'John Doe'</span></span>  

<span data-ttu-id="4c2b9-176">執行程式碼的輸出會是：</span><span class="sxs-lookup"><span data-stu-id="4c2b9-176">The output from running the code would be:</span></span>  

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

<span data-ttu-id="4c2b9-177">這些範例說明幾個重點：</span><span class="sxs-lookup"><span data-stu-id="4c2b9-177">These examples illustrate several points:</span></span>  

- <span data-ttu-id="4c2b9-178">專案方法會傳回所有狀態中的實體專案，包括已刪除的專案。</span><span class="sxs-lookup"><span data-stu-id="4c2b9-178">The Entries methods return entries for entities in all states, including Deleted.</span></span> <span data-ttu-id="4c2b9-179">將此與排除已刪除實體的本機比較。</span><span class="sxs-lookup"><span data-stu-id="4c2b9-179">Compare this to Local which excludes Deleted entities.</span></span>  
- <span data-ttu-id="4c2b9-180">使用非泛型專案方法時，會傳回所有實體類型的專案。</span><span class="sxs-lookup"><span data-stu-id="4c2b9-180">Entries for all entity types are returned when the non-generic Entries method is used.</span></span> <span data-ttu-id="4c2b9-181">使用泛型專案方法時，只會針對屬於泛型型別實例的實體傳回專案。</span><span class="sxs-lookup"><span data-stu-id="4c2b9-181">When the generic entries method is used entries are only returned for entities that are instances of the generic type.</span></span> <span data-ttu-id="4c2b9-182">這是用來取得所有 blog 的專案。</span><span class="sxs-lookup"><span data-stu-id="4c2b9-182">This was used above to get entries for all blogs.</span></span> <span data-ttu-id="4c2b9-183">它也可用來取得所有執行 IPerson 之實體的專案。</span><span class="sxs-lookup"><span data-stu-id="4c2b9-183">It was also used to get entries for all entities that implement IPerson.</span></span> <span data-ttu-id="4c2b9-184">這會示範泛型型別不一定是實際的實體型別。</span><span class="sxs-lookup"><span data-stu-id="4c2b9-184">This demonstrates that the generic type does not have to be an actual entity type.</span></span>  
- <span data-ttu-id="4c2b9-185">LINQ to Objects 可以用來篩選傳回的結果。</span><span class="sxs-lookup"><span data-stu-id="4c2b9-185">LINQ to Objects can be used to filter the results returned.</span></span> <span data-ttu-id="4c2b9-186">這是用來尋找任何類型的實體（只要修改它們的話）。</span><span class="sxs-lookup"><span data-stu-id="4c2b9-186">This was used above to find entities of any type as long as they are modified.</span></span>  

<span data-ttu-id="4c2b9-187">請注意，DbEntityEntry 實例一律包含非 null 的實體。</span><span class="sxs-lookup"><span data-stu-id="4c2b9-187">Note that DbEntityEntry instances always contain a non-null Entity.</span></span> <span data-ttu-id="4c2b9-188">關聯性專案和存根專案不會表示為 DbEntityEntry 實例，因此不需要篩選這些專案。</span><span class="sxs-lookup"><span data-stu-id="4c2b9-188">Relationship entries and stub entries are not represented as DbEntityEntry instances so there is no need to filter for these.</span></span>
