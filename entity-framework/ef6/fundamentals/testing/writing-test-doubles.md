---
title: 使用您自己的測試進行測試雙精度浮點數-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 16a8b7c0-2d23-47f4-9cc0-e2eb2e738ca3
ms.openlocfilehash: 3d8933fb5e17f8c01f3971495a1fcdb5b8cfab57
ms.sourcegitcommit: 37d0e0fd1703467918665a64837dc54ad2ec7484
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2019
ms.locfileid: "72446034"
---
# <a name="testing-with-your-own-test-doubles"></a>使用您自己的測試進行測試雙精度浮點數
> [!NOTE]
> **僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。 如果您使用的是較早版本，則不適用部分或全部的資訊。  

撰寫應用程式的測試時，通常會想要避免資料庫的叫用。  Entity Framework 可讓您藉由建立內容來達成此目的，其中包含測試所定義的行為–這會使用記憶體中的資料。  

## <a name="options-for-creating-test-doubles"></a>建立測試雙精度浮點數的選項  

有兩種不同的方法可以用來建立內容的記憶體中版本。  

- **建立您自己的測試雙精度**浮點數–此方法牽涉到撰寫您自己在記憶體中的內容和 DbSets 的執行。 這讓您有很大的控制權可以控制類別的行為，但可能牽涉到撰寫和擁有合理的程式碼數量。  
- **使用模擬架構來建立測試加倍**–使用模擬架構（例如 Moq），您可以擁有內容的記憶體內部內部部署，並在執行時間動態建立的集合。  

本文將說明如何建立您自己的測試 double。 如需使用模擬架構的詳細資訊，請參閱[使用模擬架構進行測試](mocking.md)。  

## <a name="testing-with-pre-ef6-versions"></a>使用預先 EF6 的版本進行測試  

本文中顯示的程式碼與 EF6 相容。 若要使用 EF5 和較舊版本進行測試，請參閱[使用假的內容進行測試](https://romiller.com/2012/02/14/testing-with-a-fake-dbcontext/)。  

## <a name="limitations-of-ef-in-memory-test-doubles"></a>EF 記憶體內部測試的限制雙精度浮點數  

記憶體內部測試雙精度浮點數，是提供使用 EF 之應用程式位的單元測試層級涵蓋範圍的好方法。 不過，在這麼做時，您會使用 LINQ to Objects 對記憶體中的資料執行查詢。 這可能會導致不同的行為，而不是使用 EF 的 LINQ 提供者（LINQ to Entities）將查詢轉譯為針對您的資料庫執行的 SQL。  

載入相關資料的其中一個範例是這類差異。 如果您建立一系列的 Blog，其中每個都有相關的文章，則在使用記憶體內部資料時，一律會針對每個 Blog 載入相關的文章。 不過，針對資料庫執行時，只有在您使用 Include 方法時，才會載入資料。  

基於這個理由，建議一律包含某種層級的端對端測試（除了您的單元測試以外），以確保您的應用程式能針對資料庫正確運作。  

## <a name="following-along-with-this-article"></a>遵循本文  

本文提供完整的程式代碼清單，您可以將其複製到 Visual Studio，以依照您的需要進行。 最簡單的方式是建立**單元測試專案**，而您將需要以 **.NET Framework 4.5**為目標，才能完成使用 async 的區段。  

## <a name="creating-a-context-interface"></a>建立內容介面  

我們將探討如何測試利用 EF 模型的服務。 為了能夠以記憶體內部版本來取代 EF 內容以進行測試，我們將定義 EF 內容（和它在記憶體中的雙精度浮點數）將會執行的介面。

我們要測試的服務將使用內容的 DbSet 屬性來查詢和修改資料，同時也會呼叫 SaveChanges 來將變更推送至資料庫。 因此，我們會在介面上包含這些成員。  

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

## <a name="the-ef-model"></a>EF 模型  

我們即將測試的服務會使用由 BloggingCoNtext 和 Blog 和 Post 類別所組成的 EF 模型。 這個程式碼可能是由 EF 設計工具產生，或是 Code First 模型。  

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

### <a name="implementing-the-context-interface-with-the-ef-designer"></a>使用 EF 設計工具執行內容介面  

請注意，我們的內容會實行 IBloggingCoNtext 介面。  

如果您使用 Code First 則可以直接編輯您的內容，以執行介面。 如果您使用 EF 設計工具，則必須編輯產生內容的 T4 範本。 開啟 [\<model_name @ no__t-1]。在您的 edmx 檔案底下的 CoNtext.tt 檔案，尋找下列程式碼片段，並在介面中新增，如下所示。  

``` csharp  
<#=Accessibility.ForType(container)#> partial class <#=code.Escape(container)#> : DbContext, IBloggingContext
```  

## <a name="service-to-be-tested"></a>要測試的服務  

為了示範使用記憶體內部測試進行測試，我們將針對 BlogService 撰寫幾項測試。 服務能夠建立新的 blog （AddBlog），並傳回依名稱排序的所有 Blog （GetAllBlogs）。 除了 GetAllBlogs 之外，我們也提供了一種方法，可讓您以非同步方式取得依名稱排序的所有 blog （GetAllBlogsAsync）。  

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

## <a name="creating-the-in-memory-test-doubles"></a>建立記憶體中測試雙精度浮點數  

既然我們已經有真正的 EF 模型和可使用它的服務，就可以建立可用於測試的記憶體內部測試 double。 我們已為內容建立了 TestCoNtext 測試雙精度浮點數。 在測試中，我們可以選擇我們想要的行為，以支援即將執行的測試。 在此範例中，我們只會捕捉呼叫 SaveChanges 的次數，但是您可以包含所需的任何邏輯，以驗證您要測試的案例。  

我們也建立了 TestDbSet，提供 DbSet 的記憶體內部執行。 我們已提供 DbSet 上所有方法的完整實作此實作（[尋找] 除外），但您只需要執行您的測試案例將使用的成員。  

TestDbSet 會使用我們已包含的一些其他基礎結構類別，以確保可以處理非同步查詢。  

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

### <a name="implementing-find"></a>執行尋找  

Find 方法很難以以一般方式執行。 如果您需要測試使用 Find 方法的程式碼，最簡單的方式是為每個需要支援尋找的實體類型建立測試 DbSet。 接著，您可以撰寫邏輯來尋找該特定類型的實體，如下所示。  

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

## <a name="writing-some-tests"></a>撰寫一些測試  

這就是開始測試的所有必要步驟。 下列測試會根據此內容來建立 TestCoNtext 和服務。 服務接著會用來建立新的 blog –使用 AddBlog 方法。 最後，測試會確認服務已將新的 Blog 新增至內容的 Blog 屬性，並在內容上呼叫 SaveChanges。  

這只是您可以使用記憶體中測試 double 測試的專案類型範例，而且您可以調整測試的邏輯，以及符合您需求的驗證。  

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

以下是另一個測試範例-這次是執行查詢的一個。 測試一開始會建立一個測試內容，其中包含其 [Blog] 屬性中的一些資料-請注意，資料不是依字母順序排列。 然後，我們可以根據我們的測試內容來建立 BlogService，並確保從 GetAllBlogs 取回的資料會依名稱排序。  

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

最後，我們會撰寫一個使用非同步方法的測試，以確保我們在[TestDbSet](#creating-the-in-memory-test-doubles)中所包含的非同步基礎結構運作正常。  

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
