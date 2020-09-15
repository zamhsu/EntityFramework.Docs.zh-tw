---
title: 使用您自己的測試進行測試的雙精度浮點數-EF6
description: 在 Entity Framework 6 中使用您自己的測試進行測試會加倍
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/testing/writing-test-doubles
ms.openlocfilehash: 575007c5568049e6508d1bd6435597a1acd843ab
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070428"
---
# <a name="testing-with-your-own-test-doubles"></a>使用您自己的測試進行測試的雙精度浮點數
> [!NOTE]
> **僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。 如果您使用的是較早版本，則不適用部分或全部的資訊。  

當您為應用程式撰寫測試時，通常會想要避免叫用資料庫。  Entity Framework 可讓您藉由建立內容（具有您的測試所定義的行為）來達成此目的，以利用記憶體內部資料。  

## <a name="options-for-creating-test-doubles"></a>建立測試雙精度浮點數的選項  

有兩種不同的方法可以用來建立內容的記憶體內版本。  

- **建立您自己的測試雙精度** 浮點數–此方法牽涉到撰寫您自己的內容和 DbSets 的記憶體內部執行。 這可讓您充分掌控類別的行為，但可能牽涉到撰寫和擁有合理的程式碼數量。  
- **使用模擬架構來建立測試雙精度** 浮點數（使用模擬架構 (例如) Moq），您可以讓內容的記憶體內部執行，並在執行時間以動態方式建立的設定。  

本文將討論如何建立您自己的測試雙精度浮點數。 如需使用模擬架構的詳細資訊，請參閱使用 [模擬架構進行測試](xref:ef6/fundamentals/testing/mocking)。  

## <a name="testing-with-pre-ef6-versions"></a>使用預先 EF6 的版本進行測試  

本文所顯示的程式碼與 EF6 相容。 若要使用 EF5 和更早版本進行測試，請參閱 [使用假內容進行測試](https://romiller.com/2012/02/14/testing-with-a-fake-dbcontext/)。  

## <a name="limitations-of-ef-in-memory-test-doubles"></a>EF in memory 測試雙精度浮點數的限制  

記憶體內部測試雙精度浮點數，可提供應用程式中使用 EF 之位的單元測試層級涵蓋範圍。 但是，當您這麼做時，您會使用 LINQ to Objects 針對記憶體中的資料執行查詢。 這可能會導致不同的行為，而不是使用 EF 的 LINQ 提供者 (LINQ to Entities) 將查詢轉譯成針對您的資料庫執行的 SQL。  

這種差異的其中一個範例是載入相關資料。 如果您建立一系列的 Blog，其中每個都有相關的貼文，則在使用記憶體內部資料時，一律會針對每個 Blog 載入相關的文章。 不過，針對資料庫執行時，只有當您使用 Include 方法時，才會載入資料。  

基於這個理由，建議您一律包含部分端對端測試 (除了您的單元測試) ，以確保您的應用程式能針對資料庫正確運作。  

## <a name="following-along-with-this-article"></a>遵循本文  

本文提供完整的程式代碼清單，可讓您視需要複製到 Visual Studio 以進行追蹤。 最簡單的方式是建立 **單元測試專案** ，您必須將 **.NET Framework 4.5** 為目標，才能完成使用 async 的區段。  

## <a name="creating-a-context-interface"></a>建立內容介面  

我們將探討如何測試利用 EF 模型的服務。 為了能夠以記憶體內部版本取代 EF coNtext 以進行測試，我們將定義 EF coNtext (的介面，並將它的記憶體內部雙精度) 將會執行。

我們要測試的服務會使用內容的 DbSet 屬性來查詢和修改資料，同時也呼叫 SaveChanges 來將變更推送至資料庫。 因此，我們會在介面上包含這些成員。  

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

我們即將測試的服務會使用由 BloggingCoNtext 和 Blog 和 Post 類別組成的 EF 模型。 這段程式碼可能由 EF 設計工具產生，或為 Code First 模型。  

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

### <a name="implementing-the-context-interface-with-the-ef-designer"></a>使用 EF 設計工具來執行內容介面  

請注意，我們的內容會實 IBloggingCoNtext 介面。  

如果您使用 Code First 則可以直接編輯內容來執行介面。 如果您使用 EF 設計工具，則必須編輯產生內容的 T4 範本。 開啟 \<model_name\> 。內嵌在您 edmx 檔案底下的 CoNtext.tt 檔案，請尋找下列程式碼片段，並新增至介面中，如下所示。  

``` csharp  
<#=Accessibility.ForType(container)#> partial class <#=code.Escape(container)#> : DbContext, IBloggingContext
```  

## <a name="service-to-be-tested"></a>要測試的服務  

為了示範如何使用記憶體內部測試進行測試，我們將為 BlogService 撰寫幾項測試。 服務能夠建立新的 blog (AddBlog) 並傳回依名稱 (GetAllBlogs) 排序的所有 Blog。 除了 GetAllBlogs 之外，我們也提供了一種方法，會以非同步方式取得依名稱排序的所有 blog (GetAllBlogsAsync) 。  

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

## <a name="creating-the-in-memory-test-doubles"></a>建立記憶體內部測試雙精度浮點數  

既然我們已有真正的 EF 模型和可使用它的服務，現在就可以建立可用於測試的記憶體內部測試雙精度浮點數。 我們為內容建立了 TestCoNtext 的測試雙精度浮點數。 在測試中，我們可以選擇我們想要的行為，以便支援我們即將執行的測試。 在此範例中，我們只會捕捉呼叫 SaveChanges 的次數，但您可以包含任何需要的邏輯來驗證您要測試的案例。  

我們也建立了可提供 DbSet 記憶體內部執行的 TestDbSet。 我們已針對 DbSet (上的所有方法提供完整的實作此實作，除了 Find) 之外，您只需要執行測試案例將使用的成員。  

TestDbSet 會使用我們所包含的其他一些基礎結構類別，以確保可以處理非同步查詢。  

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

Find 方法很難以一般方式來執行。 如果您需要測試利用 Find 方法的程式碼，最簡單的方式是為每個需要支援 find 的實體類型建立測試 DbSet。 然後您可以撰寫邏輯以找出該特定類型的實體，如下所示。  

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

## <a name="writing-some-tests"></a>撰寫某些測試  

這就是開始測試所需的一切。 下列測試會根據此內容建立 TestCoNtext 和服務。 接著，服務會用來建立新的 blog –使用 AddBlog 方法。 最後，測試會確認服務已將新的 Blog 新增至內容的 Blog 屬性，並在內容上呼叫 SaveChanges。  

這只是您可以使用記憶體內部測試進行測試的類型範例，您可以調整測試的邏輯，以及符合您需求的驗證。  

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

以下是另一個測試範例-這次會執行查詢。 測試一開始會在其 Blog 屬性中建立含有一些資料的測試內容-請注意，資料不是依字母順序排列。 接著，我們可以根據我們的測試內容來建立 BlogService，並確保我們從 GetAllBlogs 取回的資料會依名稱排序。  

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

最後，我們會撰寫一個使用 async 方法的測試，以確保我們包含在 [TestDbSet](#creating-the-in-memory-test-doubles) 中的非同步基礎結構可以運作。  

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
