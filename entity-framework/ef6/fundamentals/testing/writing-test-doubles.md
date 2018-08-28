---
title: 使用您自己的測試替身-EF6 測試
author: divega
ms.date: 2016-10-23
ms.assetid: 16a8b7c0-2d23-47f4-9cc0-e2eb2e738ca3
ms.openlocfilehash: fa63c483e0a55b6cbd43382f68ab9592f3c1768b
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42997213"
---
# <a name="testing-with-your-own-test-doubles"></a><span data-ttu-id="ac863-102">使用您自己的測試替身進行測試</span><span class="sxs-lookup"><span data-stu-id="ac863-102">Testing with your own test doubles</span></span>
> [!NOTE]
> <span data-ttu-id="ac863-103">**僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。</span><span class="sxs-lookup"><span data-stu-id="ac863-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="ac863-104">如果您使用的是較早版本，則不適用部分或全部的資訊。</span><span class="sxs-lookup"><span data-stu-id="ac863-104">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="ac863-105">撰寫您的應用程式的測試時，通常最好避免達到資料庫。</span><span class="sxs-lookup"><span data-stu-id="ac863-105">When writing tests for your application it is often desirable to avoid hitting the database.</span></span>  <span data-ttu-id="ac863-106">Entity Framework 可讓您達成此目的所建立的內容 – 定義您的測試-的行為，以便使用的記憶體中的資料。</span><span class="sxs-lookup"><span data-stu-id="ac863-106">Entity Framework allows you to achieve this by creating a context – with behavior defined by your tests – that makes use of in-memory data.</span></span>  

## <a name="options-for-creating-test-doubles"></a><span data-ttu-id="ac863-107">建立測試替身的選項</span><span class="sxs-lookup"><span data-stu-id="ac863-107">Options for creating test doubles</span></span>  

<span data-ttu-id="ac863-108">有兩種不同的方法，可用來建立您的內容是記憶體中的舊版。</span><span class="sxs-lookup"><span data-stu-id="ac863-108">There are two different approaches that can be used to create an in-memory version of your context.</span></span>  

- <span data-ttu-id="ac863-109">**建立您自己的測試替身**– 這種方法涉及撰寫您自己的記憶體中實作您的內容和 DbSets。</span><span class="sxs-lookup"><span data-stu-id="ac863-109">**Create your own test doubles** – This approach involves writing your own in-memory implementation of your context and DbSets.</span></span> <span data-ttu-id="ac863-110">這可讓您控制類別如何運作，但是可以寫入和擁有合理的程式碼牽涉到很多。</span><span class="sxs-lookup"><span data-stu-id="ac863-110">This gives you a lot of control over how the classes behave but can involve writing and owning a reasonable amount of code.</span></span>  
- <span data-ttu-id="ac863-111">**使用模擬的架構來建立測試替身**– 使用模擬的架構 （例如 Moq) 您可以有您的記憶體中實作內容並在執行階段以動態方式建立您的設定。</span><span class="sxs-lookup"><span data-stu-id="ac863-111">**Use a mocking framework to create test doubles** – Using a mocking framework (such as Moq) you can have the in-memory implementations of you context and sets created dynamically at runtime for you.</span></span>  

<span data-ttu-id="ac863-112">這篇文章將會處理建立您自己的測試 double。</span><span class="sxs-lookup"><span data-stu-id="ac863-112">This article will deal with creating your own test double.</span></span> <span data-ttu-id="ac863-113">如需使用模擬架構的詳細資訊，請參閱[測試模擬 (Mock) 架構](mocking.md)。</span><span class="sxs-lookup"><span data-stu-id="ac863-113">For information on using a mocking framework see [Testing with a Mocking Framework](mocking.md).</span></span>  

## <a name="testing-with-pre-ef6-versions"></a><span data-ttu-id="ac863-114">測試前 EF6 版本</span><span class="sxs-lookup"><span data-stu-id="ac863-114">Testing with pre-EF6 versions</span></span>  

<span data-ttu-id="ac863-115">這篇文章中所顯示的程式碼適用於 EF6。</span><span class="sxs-lookup"><span data-stu-id="ac863-115">The code shown in this article is compatible with EF6.</span></span> <span data-ttu-id="ac863-116">使用 EF5 和更舊版本進行測試，請參閱[測試與假的內容](http://romiller.com/2012/02/14/testing-with-a-fake-dbcontext/)。</span><span class="sxs-lookup"><span data-stu-id="ac863-116">For testing with EF5 and earlier version see [Testing with a Fake Context](http://romiller.com/2012/02/14/testing-with-a-fake-dbcontext/).</span></span>  

## <a name="limitations-of-ef-in-memory-test-doubles"></a><span data-ttu-id="ac863-117">EF 記憶體測試替身的限制</span><span class="sxs-lookup"><span data-stu-id="ac863-117">Limitations of EF in-memory test doubles</span></span>  

<span data-ttu-id="ac863-118">記憶體中的測試替身可提供單元測試的應用程式使用 EF 的位元層級的涵蓋範圍的好方法。</span><span class="sxs-lookup"><span data-stu-id="ac863-118">In-memory test doubles can be a good way to provide unit test level coverage of bits of your application that use EF.</span></span> <span data-ttu-id="ac863-119">不過，這麼做時您會使用 LINQ to Objects 來針對記憶體中的資料執行查詢。</span><span class="sxs-lookup"><span data-stu-id="ac863-119">However, when doing this you are using LINQ to Objects to execute queries against in-memory data.</span></span> <span data-ttu-id="ac863-120">這可能會導致不同的行為，比使用 EF 的 LINQ 提供者 (LINQ to Entities)，將查詢轉譯為針對您的資料庫執行的 SQL。</span><span class="sxs-lookup"><span data-stu-id="ac863-120">This can result in different behavior than using EF’s LINQ provider (LINQ to Entities) to translate queries into SQL that is run against your database.</span></span>  

<span data-ttu-id="ac863-121">這類差異的其中一個範例會載入相關的資料。</span><span class="sxs-lookup"><span data-stu-id="ac863-121">One example of such a difference is loading related data.</span></span> <span data-ttu-id="ac863-122">如果您建立一系列部落格的每個有相關的文章，然後使用記憶體中的資料時的相關的文章一律要載入的每個部落格。</span><span class="sxs-lookup"><span data-stu-id="ac863-122">If you create a series of Blogs that each have related Posts, then when using in-memory data the related Posts will always be loaded for each Blog.</span></span> <span data-ttu-id="ac863-123">不過，對資料庫執行時載入的資料將僅如果您使用 Include 方法。</span><span class="sxs-lookup"><span data-stu-id="ac863-123">However, when running against a database the data will only be loaded if you use the Include method.</span></span>  

<span data-ttu-id="ac863-124">基於這個理由，建議一律包含某種程度的端對端測試 若要確保您的應用程式運作正確地針對資料庫的 電子郵件 （除了您的單元測試）。</span><span class="sxs-lookup"><span data-stu-id="ac863-124">For this reason, it is recommended to always include some level of end-to-end testing (in addition to your unit tests) to ensure your application works correctly against a database.</span></span>  

## <a name="following-along-with-this-article"></a><span data-ttu-id="ac863-125">觀摩這份文件</span><span class="sxs-lookup"><span data-stu-id="ac863-125">Following along with this article</span></span>  

<span data-ttu-id="ac863-126">這篇文章可讓您可以將它複製到要跟著做如果您想要的 Visual Studio 的完整程式碼清單。</span><span class="sxs-lookup"><span data-stu-id="ac863-126">This article gives complete code listings that you can copy into Visual Studio to follow along if you wish.</span></span> <span data-ttu-id="ac863-127">它是最簡單的方式建立**單元測試專案**，您必須到目標 **.NET Framework 4.5**完成使用非同步的區段。</span><span class="sxs-lookup"><span data-stu-id="ac863-127">It's easiest to create a **Unit Test Project** and you will need to target **.NET Framework 4.5** to complete the sections that use async.</span></span>  

## <a name="creating-a-context-interface"></a><span data-ttu-id="ac863-128">建立內容介面</span><span class="sxs-lookup"><span data-stu-id="ac863-128">Creating a context interface</span></span>  

<span data-ttu-id="ac863-129">我們要查看測試服務會使用 EF 模型。</span><span class="sxs-lookup"><span data-stu-id="ac863-129">We're going to look at testing a service that makes use of an EF model.</span></span> <span data-ttu-id="ac863-130">若要能在我們的 EF 內容取代為測試版記憶體中，我們會定義介面我們 EF 內容 （和其記憶體雙精度浮點數） 都將 imeplement。</span><span class="sxs-lookup"><span data-stu-id="ac863-130">In order to be able to replace our EF context with an in-memory version for testing, we'll define an interface that our EF context (and it's in-memory double) will imeplement.</span></span>  

<span data-ttu-id="ac863-131">我們要測試此服務將查詢和修改使用我們的內容的 DbSet 屬性的資料，並同時也呼叫 SaveChanges 將變更推送至資料庫。</span><span class="sxs-lookup"><span data-stu-id="ac863-131">The service we are going to test will query and modify data using the DbSet properties of our context and also call SaveChanges to push changes to the database.</span></span> <span data-ttu-id="ac863-132">因此我們加入這些成員在介面上。</span><span class="sxs-lookup"><span data-stu-id="ac863-132">So we're including these members on the interface.</span></span>  

``` csharp
using System.Data.Entity;

namespace TestingDemo
{
    public interface IBloggingContext
    {
        DbSet<Blog> Blogs { get; }
        DbSet<Post> Posts { get; }
        int SaveChanges();
    }
}
```  

## <a name="the-ef-model"></a><span data-ttu-id="ac863-133">EF 模型</span><span class="sxs-lookup"><span data-stu-id="ac863-133">The EF model</span></span>  

<span data-ttu-id="ac863-134">我們要測試此服務可讓您可使用的 EF 模型為 [bloggingcontext] 和部落格和文章類別組成。</span><span class="sxs-lookup"><span data-stu-id="ac863-134">The service we're going to test makes use of an EF model made up of the BloggingContext and the Blog and Post classes.</span></span> <span data-ttu-id="ac863-135">此程式碼可能會有 EF 設計工具所產生或 Code First 模型。</span><span class="sxs-lookup"><span data-stu-id="ac863-135">This code may have been generated by the EF Designer or be a Code First model.</span></span>  

``` csharp
using System.Collections.Generic;
using System.Data.Entity;

namespace TestingDemo
{
    public class BloggingContext : DbContext, IBloggingContext
    {
        public DbSet<Blog> Blogs { get; set; }
        public DbSet<Post> Posts { get; set; }
    }

    public class Blog
    {
        public int BlogId { get; set; }
        public string Name { get; set; }
        public string Url { get; set; }

        public virtual List<Post> Posts { get; set; }
    }

    public class Post
    {
        public int PostId { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }

        public int BlogId { get; set; }
        public virtual Blog Blog { get; set; }
    }
}
```  

### <a name="implementing-the-context-interface-with-the-ef-designer"></a><span data-ttu-id="ac863-136">實作內容介面與 EF 設計工具</span><span class="sxs-lookup"><span data-stu-id="ac863-136">Implementing the context interface with the EF Designer</span></span>  

<span data-ttu-id="ac863-137">請注意我們的內容實作 IBloggingContext 介面。</span><span class="sxs-lookup"><span data-stu-id="ac863-137">Note that our context implements the IBloggingContext interface.</span></span>  

<span data-ttu-id="ac863-138">如果您使用 Code First，您可以編輯您的內容，直接以實作的介面。</span><span class="sxs-lookup"><span data-stu-id="ac863-138">If you are using Code First then you can edit your context directly to implement the interface.</span></span> <span data-ttu-id="ac863-139">如果您使用 EF 設計工具將需要編輯 T4 範本產生您的內容。</span><span class="sxs-lookup"><span data-stu-id="ac863-139">If you are using the EF Designer then you’ll need to edit the T4 template that generates your context.</span></span> <span data-ttu-id="ac863-140">開啟\<model_name\>。Context.tt 檔案底下您 edmx 檔案，尋找下列程式碼片段，並加入介面中所示。</span><span class="sxs-lookup"><span data-stu-id="ac863-140">Open up the \<model_name\>.Context.tt file that is nested under you edmx file, find the following fragment of code and add in the interface as shown.</span></span>  

``` csharp  
<#=Accessibility.ForType(container)#> partial class <#=code.Escape(container)#> : DbContext, IBloggingContext
```  

## <a name="service-to-be-tested"></a><span data-ttu-id="ac863-141">要測試服務</span><span class="sxs-lookup"><span data-stu-id="ac863-141">Service to be tested</span></span>  

<span data-ttu-id="ac863-142">為了示範使用記憶體中測試替身，我們將會撰寫 BlogService 幾個測試的測試。</span><span class="sxs-lookup"><span data-stu-id="ac863-142">To demonstrate testing with in-memory test doubles we are going to be writing a couple of tests for a BlogService.</span></span> <span data-ttu-id="ac863-143">服務是能夠建立新的部落格 (AddBlog)，並傳回所有部落格會依名稱 (GetAllBlogs)。</span><span class="sxs-lookup"><span data-stu-id="ac863-143">The service is capable of creating new blogs (AddBlog) and returning all Blogs ordered by name (GetAllBlogs).</span></span> <span data-ttu-id="ac863-144">除了 GetAllBlogs，我們也提供的方法，將會以非同步方式取得依名稱 (GetAllBlogsAsync) 的所有部落格。</span><span class="sxs-lookup"><span data-stu-id="ac863-144">In addition to GetAllBlogs, we’ve also provided a method that will asynchronously get all blogs ordered by name (GetAllBlogsAsync).</span></span>  

``` csharp
using System.Collections.Generic;
using System.Data.Entity;
using System.Linq;
using System.Threading.Tasks;

namespace TestingDemo
{
    public class BlogService
    {
        private IBloggingContext _context;

        public BlogService(IBloggingContext context)
        {
            _context = context;
        }

        public Blog AddBlog(string name, string url)
        {
            var blog = new Blog { Name = name, Url = url };
            _context.Blogs.Add(blog);
            _context.SaveChanges();

            return blog;
        }

        public List<Blog> GetAllBlogs()
        {
            var query = from b in _context.Blogs
                        orderby b.Name
                        select b;

            return query.ToList();
        }

        public async Task<List<Blog>> GetAllBlogsAsync()
        {
            var query = from b in _context.Blogs
                        orderby b.Name
                        select b;

            return await query.ToListAsync();
        }
    }
}
```  

<span data-ttu-id="ac863-145"><a name="creating-the-in-memory-test-doubles"/> # # 來建立記憶體中測試加倍</span><span class="sxs-lookup"><span data-stu-id="ac863-145"><a name="creating-the-in-memory-test-doubles"/> ## Creating the in-memory test doubles</span></span>  

<span data-ttu-id="ac863-146">既然我們有實際的 EF 模型和服務可以使用它，它是建立於記憶體中的測試 double 我們可以用來測試的時間。</span><span class="sxs-lookup"><span data-stu-id="ac863-146">Now that we have the real EF model and the service that can use it, it's time to create the in-memory test double that we can use for testing.</span></span> <span data-ttu-id="ac863-147">我們建立了 TestContext 測試 double 針對我們的內容。</span><span class="sxs-lookup"><span data-stu-id="ac863-147">We've created a TestContext test double for our context.</span></span> <span data-ttu-id="ac863-148">在測試替身，我們可以選擇我們想要以支援測試的行為，我們即將執行。</span><span class="sxs-lookup"><span data-stu-id="ac863-148">In test doubles we get to choose the behavior we want in order to support the tests we are going to run.</span></span> <span data-ttu-id="ac863-149">在此範例中我們只所擷取的次數，好讓呼叫 SaveChanges，但您可以包含任何邏輯才能確認您要測試的案例。</span><span class="sxs-lookup"><span data-stu-id="ac863-149">In this example we're just capturing the number of times SaveChanges is called, but you can include whatever logic is needed to verify the scenario you are testing.</span></span>  

<span data-ttu-id="ac863-150">我們也建立了提供記憶體中實作的 DbSet TestDbSet。</span><span class="sxs-lookup"><span data-stu-id="ac863-150">We've also created a TestDbSet that provides an in-memory implementation of DbSet.</span></span> <span data-ttu-id="ac863-151">我們提供的所有方法的完整實作所上 DbSet （除了尋找），但您只需要實作您的測試案例會使用的成員。</span><span class="sxs-lookup"><span data-stu-id="ac863-151">We've provided a complete implemention for all the methods on DbSet (except for Find), but you only need to implement the members that your test scenario will use.</span></span>  

<span data-ttu-id="ac863-152">TestDbSet 利用我們包含了以確保非同步查詢都可以處理的一些其他基礎結構類別。</span><span class="sxs-lookup"><span data-stu-id="ac863-152">TestDbSet makes use of some other infrastructure classes that we've included to ensure that async queries can be processed.</span></span>  

``` csharp
using System;
using System.Collections.Generic;
using System.Collections.ObjectModel;
using System.Data.Entity;
using System.Data.Entity.Infrastructure;
using System.Linq;
using System.Linq.Expressions;
using System.Threading;
using System.Threading.Tasks;

namespace TestingDemo
{
    public class TestContext : IBloggingContext
    {
        public TestContext()
        {
            this.Blogs = new TestDbSet<Blog>();
            this.Posts = new TestDbSet<Post>();
        }

        public DbSet<Blog> Blogs { get; set; }
        public DbSet<Post> Posts { get; set; }
        public int SaveChangesCount { get; private set; }
        public int SaveChanges()
        {
            this.SaveChangesCount++;
            return 1;
        }
    }

    public class TestDbSet<TEntity> : DbSet<TEntity>, IQueryable, IEnumerable<TEntity>, IDbAsyncEnumerable<TEntity>
        where TEntity : class
    {
        ObservableCollection<TEntity> _data;
        IQueryable _query;

        public TestDbSet()
        {
            _data = new ObservableCollection<TEntity>();
            _query = _data.AsQueryable();
        }

        public override TEntity Add(TEntity item)
        {
            _data.Add(item);
            return item;
        }

        public override TEntity Remove(TEntity item)
        {
            _data.Remove(item);
            return item;
        }

        public override TEntity Attach(TEntity item)
        {
            _data.Add(item);
            return item;
        }

        public override TEntity Create()
        {
            return Activator.CreateInstance<TEntity>();
        }

        public override TDerivedEntity Create<TDerivedEntity>()
        {
            return Activator.CreateInstance<TDerivedEntity>();
        }

        public override ObservableCollection<TEntity> Local
        {
            get { return _data; }
        }

        Type IQueryable.ElementType
        {
            get { return _query.ElementType; }
        }

        Expression IQueryable.Expression
        {
            get { return _query.Expression; }
        }

        IQueryProvider IQueryable.Provider
        {
            get { return new TestDbAsyncQueryProvider<TEntity>(_query.Provider); }
        }

        System.Collections.IEnumerator System.Collections.IEnumerable.GetEnumerator()
        {
            return _data.GetEnumerator();
        }

        IEnumerator<TEntity> IEnumerable<TEntity>.GetEnumerator()
        {
            return _data.GetEnumerator();
        }

        IDbAsyncEnumerator<TEntity> IDbAsyncEnumerable<TEntity>.GetAsyncEnumerator()
        {
            return new TestDbAsyncEnumerator<TEntity>(_data.GetEnumerator());
        }
    }

    internal class TestDbAsyncQueryProvider<TEntity> : IDbAsyncQueryProvider
    {
        private readonly IQueryProvider _inner;

        internal TestDbAsyncQueryProvider(IQueryProvider inner)
        {
            _inner = inner;
        }

        public IQueryable CreateQuery(Expression expression)
        {
            return new TestDbAsyncEnumerable<TEntity>(expression);
        }

        public IQueryable<TElement> CreateQuery<TElement>(Expression expression)
        {
            return new TestDbAsyncEnumerable<TElement>(expression);
        }

        public object Execute(Expression expression)
        {
            return _inner.Execute(expression);
        }

        public TResult Execute<TResult>(Expression expression)
        {
            return _inner.Execute<TResult>(expression);
        }

        public Task<object> ExecuteAsync(Expression expression, CancellationToken cancellationToken)
        {
            return Task.FromResult(Execute(expression));
        }

        public Task<TResult> ExecuteAsync<TResult>(Expression expression, CancellationToken cancellationToken)
        {
            return Task.FromResult(Execute<TResult>(expression));
        }
    }

    internal class TestDbAsyncEnumerable<T> : EnumerableQuery<T>, IDbAsyncEnumerable<T>, IQueryable<T>
    {
        public TestDbAsyncEnumerable(IEnumerable<T> enumerable)
            : base(enumerable)
        { }

        public TestDbAsyncEnumerable(Expression expression)
            : base(expression)
        { }

        public IDbAsyncEnumerator<T> GetAsyncEnumerator()
        {
            return new TestDbAsyncEnumerator<T>(this.AsEnumerable().GetEnumerator());
        }

        IDbAsyncEnumerator IDbAsyncEnumerable.GetAsyncEnumerator()
        {
            return GetAsyncEnumerator();
        }

        IQueryProvider IQueryable.Provider
        {
            get { return new TestDbAsyncQueryProvider<T>(this); }
        }
    }

    internal class TestDbAsyncEnumerator<T> : IDbAsyncEnumerator<T>
    {
        private readonly IEnumerator<T> _inner;

        public TestDbAsyncEnumerator(IEnumerator<T> inner)
        {
            _inner = inner;
        }

        public void Dispose()
        {
            _inner.Dispose();
        }

        public Task<bool> MoveNextAsync(CancellationToken cancellationToken)
        {
            return Task.FromResult(_inner.MoveNext());
        }

        public T Current
        {
            get { return _inner.Current; }
        }

        object IDbAsyncEnumerator.Current
        {
            get { return Current; }
        }
    }
}
```  

### <a name="implementing-find"></a><span data-ttu-id="ac863-153">實作尋找</span><span class="sxs-lookup"><span data-stu-id="ac863-153">Implementing Find</span></span>  

<span data-ttu-id="ac863-154">Find 方法很難實作一般的方式。</span><span class="sxs-lookup"><span data-stu-id="ac863-154">The Find method is difficult to implement in a generic fashion.</span></span> <span data-ttu-id="ac863-155">如果您要測試程式碼，讓使用 Find 方法，它是最簡單的方式建立的每個需要支援實體類型的 DbSet 尋找的測試。</span><span class="sxs-lookup"><span data-stu-id="ac863-155">If you need to test code that makes use of the Find method it is easiest to create a test DbSet for each of the entity types that need to support find.</span></span> <span data-ttu-id="ac863-156">然後，您可以撰寫邏輯，以尋找該特定類型的實體，如下所示。</span><span class="sxs-lookup"><span data-stu-id="ac863-156">You can then write logic to find that particular type of entity, as shown below.</span></span>  

``` csharp
using System.Linq;

namespace TestingDemo
{
    class TestBlogDbSet : TestDbSet<Blog>
    {
        public override Blog Find(params object[] keyValues)
        {
            var id = (int)keyValues.Single();
            return this.SingleOrDefault(b => b.BlogId == id);
        }
    }
}
```  

## <a name="writing-some-tests"></a><span data-ttu-id="ac863-157">編寫一些測試</span><span class="sxs-lookup"><span data-stu-id="ac863-157">Writing some tests</span></span>  

<span data-ttu-id="ac863-158">這是我們只需要啟動測試。</span><span class="sxs-lookup"><span data-stu-id="ac863-158">That’s all we need to do to start testing.</span></span> <span data-ttu-id="ac863-159">下列測試建立 TestContext，然後根據此環境的服務。</span><span class="sxs-lookup"><span data-stu-id="ac863-159">The following test creates a TestContext and then a service based on this context.</span></span> <span data-ttu-id="ac863-160">服務接著會用來建立新的部落格 – 使用 AddBlog 方法中。</span><span class="sxs-lookup"><span data-stu-id="ac863-160">The service is then used to create a new blog – using the AddBlog method.</span></span> <span data-ttu-id="ac863-161">最後，測試會驗證服務加入新的部落格內容的部落格屬性，並且在內容上呼叫 SaveChanges。</span><span class="sxs-lookup"><span data-stu-id="ac863-161">Finally, the test verifies that the service added a new Blog to the context's Blogs property and called SaveChanges on the context.</span></span>  

<span data-ttu-id="ac863-162">這是只是您可以使用記憶體中測試替身進行測試的項目類型的範例，您可以調整的測試替身，以符合您需求的驗證邏輯。</span><span class="sxs-lookup"><span data-stu-id="ac863-162">This is just an example of the types of things you can test with an in-memory test double and you can adjust the logic of the test doubles and the verification to meet your requirements.</span></span>  

``` csharp
using Microsoft.VisualStudio.TestTools.UnitTesting;
using System.Linq;

namespace TestingDemo
{
    [TestClass]
    public class NonQueryTests
    {
        [TestMethod]
        public void CreateBlog_saves_a_blog_via_context()
        {
            var context = new TestContext();

            var service = new BlogService(context);
            service.AddBlog("ADO.NET Blog", "http://blogs.msdn.com/adonet");

            Assert.AreEqual(1, context.Blogs.Count());
            Assert.AreEqual("ADO.NET Blog", context.Blogs.Single().Name);
            Assert.AreEqual("http://blogs.msdn.com/adonet", context.Blogs.Single().Url);
            Assert.AreEqual(1, context.SaveChangesCount);
        }
    }
}
```  

<span data-ttu-id="ac863-163">以下是另一個測試-這次會執行查詢的其中一個範例。</span><span class="sxs-lookup"><span data-stu-id="ac863-163">Here is another example of a test - this time one that performs a query.</span></span> <span data-ttu-id="ac863-164">測試一開始會建立含有一些資料在其部落格屬性-請注意，資料不是依字母順序中的測試內容。</span><span class="sxs-lookup"><span data-stu-id="ac863-164">The test starts by creating a test context with some data in its Blog property - note that the data is not in alphabetical order.</span></span> <span data-ttu-id="ac863-165">接著，我們可以建立 BlogService，根據我們的測試內容，並確保我們從 GetAllBlogs 回取得的資料依名稱排序。</span><span class="sxs-lookup"><span data-stu-id="ac863-165">We can then create a BlogService based on our test context and ensure that the data we get back from GetAllBlogs is ordered by name.</span></span>  

``` csharp
using Microsoft.VisualStudio.TestTools.UnitTesting;

namespace TestingDemo
{
    [TestClass]
    public class QueryTests
    {
        [TestMethod]
        public void GetAllBlogs_orders_by_name()
        {
            var context = new TestContext();
            context.Blogs.Add(new Blog { Name = "BBB" });
            context.Blogs.Add(new Blog { Name = "ZZZ" });
            context.Blogs.Add(new Blog { Name = "AAA" });

            var service = new BlogService(context);
            var blogs = service.GetAllBlogs();

            Assert.AreEqual(3, blogs.Count);
            Assert.AreEqual("AAA", blogs[0].Name);
            Assert.AreEqual("BBB", blogs[1].Name);
            Assert.AreEqual("ZZZ", blogs[2].Name);
        }
    }
}
```  

<span data-ttu-id="ac863-166">最後，我們將撰寫一個使用我們的非同步方法，以確保我們納入的非同步處理基礎結構的多個測試[TestDbSet](#creating-the-in-memory-test-doubles)運作。</span><span class="sxs-lookup"><span data-stu-id="ac863-166">Finally, we'll write one more test that uses our async method to ensure that the async infrastructure we included in [TestDbSet](#creating-the-in-memory-test-doubles) is working.</span></span>  

``` csharp
using Microsoft.VisualStudio.TestTools.UnitTesting;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;

namespace TestingDemo
{
    [TestClass]
    public class AsyncQueryTests
    {
        [TestMethod]
        public async Task GetAllBlogsAsync_orders_by_name()
        {
            var context = new TestContext();
            context.Blogs.Add(new Blog { Name = "BBB" });
            context.Blogs.Add(new Blog { Name = "ZZZ" });
            context.Blogs.Add(new Blog { Name = "AAA" });

            var service = new BlogService(context);
            var blogs = await service.GetAllBlogsAsync();

            Assert.AreEqual(3, blogs.Count);
            Assert.AreEqual("AAA", blogs[0].Name);
            Assert.AreEqual("BBB", blogs[1].Name);
            Assert.AreEqual("ZZZ", blogs[2].Name);
        }
    }
}
```  
