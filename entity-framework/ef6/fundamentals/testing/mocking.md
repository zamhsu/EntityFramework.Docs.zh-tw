---
title: 測試的模擬架構-EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: bd66a638-d245-44d4-8e71-b9c6cb335cc7
caps.latest.revision: 3
ms.openlocfilehash: 7529929a3ed3906e1201c0899f2fb8b959ec9ed2
ms.sourcegitcommit: 390f3a37bc55105ed7cc5b0e0925b7f9c9e80ba6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2018
ms.locfileid: "39120467"
---
# <a name="testing-with-a-mocking-framework"></a>測試的模擬架構
> [!NOTE]
> **僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。 如果您使用的是較早版本，則不適用部分或全部的資訊。  

撰寫您的應用程式的測試時，通常最好避免達到資料庫。  Entity Framework 可讓您達成此目的所建立的內容 – 定義您的測試-的行為，以便使用的記憶體中的資料。  

## <a name="options-for-creating-test-doubles"></a>建立測試替身的選項  

有兩種不同的方法，可用來建立您的內容是記憶體中的舊版。  

- **建立您自己的測試替身**– 這種方法涉及撰寫您自己的記憶體中實作您的內容和 DbSets。 這可讓您控制類別如何運作，但是可以寫入和擁有合理的程式碼牽涉到很多。  
- **使用模擬的架構來建立測試替身**– 使用模擬的架構 （例如 Moq) 您可以有您的記憶體中實作內容並在執行階段以動態方式建立您的設定。  

這篇文章將會處理使用的模擬架構。 建立您自己的測試替身請參閱[測試您自己的測試替身](writing-test-doubles.md)。  

我們要使用 Moq 來示範如何使用 EF 使用的模擬架構。 若要取得 Moq 最簡單的方式是安裝[Moq 封裝從 NuGet](http://nuget.org/packages/Moq/)。  

## <a name="testing-with-pre-ef6-versions"></a>測試前 EF6 版本  

這篇文章中所示的案例是依據我們對 DbSet 在 EF6 中進行一些變更。 使用 EF5 和更舊版本進行測試，請參閱[測試與假的內容](http://romiller.com/2012/02/14/testing-with-a-fake-dbcontext/)。  

## <a name="limitations-of-ef-in-memory-test-doubles"></a>EF 記憶體測試替身的限制  

記憶體中的測試替身可提供單元測試的應用程式使用 EF 的位元層級的涵蓋範圍的好方法。 不過，這麼做時您會使用 LINQ to Objects 來針對記憶體中的資料執行查詢。 這可能會導致不同的行為，比使用 EF 的 LINQ 提供者 (LINQ to Entities)，將查詢轉譯為針對您的資料庫執行的 SQL。  

這類差異的其中一個範例會載入相關的資料。 如果您建立一系列部落格的每個有相關的文章，然後使用記憶體中的資料時的相關的文章一律要載入的每個部落格。 不過，對資料庫執行時載入的資料將僅如果您使用 Include 方法。  

基於這個理由，建議一律包含某種程度的端對端測試 若要確保您的應用程式運作正確地針對資料庫的 電子郵件 （除了您的單元測試）。  

## <a name="following-along-with-this-article"></a>觀摩這份文件  

這篇文章可讓您可以將它複製到要跟著做如果您想要的 Visual Studio 的完整程式碼清單。 它是最簡單的方式建立**單元測試專案**，您必須到目標 **.NET Framework 4.5**完成使用非同步的區段。  

## <a name="the-ef-model"></a>EF 模型  

我們要測試此服務可讓您可使用的 EF 模型為 [bloggingcontext] 和部落格和文章類別組成。 此程式碼可能會有 EF 設計工具所產生或 Code First 模型。  

``` csharp
using System.Collections.Generic;
using System.Data.Entity;

namespace TestingDemo
{
    public class BloggingContext : DbContext
    {
        public virtual DbSet<Blog> Blogs { get; set; }
        public virtual DbSet<Post> Posts { get; set; }
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

### <a name="virtual-dbset-properties-with-ef-designer"></a>使用 EF 設計工具的虛擬 DbSet 屬性  

請注意，在內容上的 DbSet 屬性會標示為虛擬。 這可讓模擬的架構，以衍生自我們的內容，並覆寫這些屬性與模擬 （mock） 實作。  

如果您使用 Code First，然後您可以直接編輯您的類別。 如果您使用 EF 設計工具將需要編輯 T4 範本產生您的內容。 開啟\<model_name\>。Context.tt 檔案底下您 edmx 檔案，尋找下列程式碼片段，並新增虛擬關鍵字中所示。  

``` csharp
public string DbSet(EntitySet entitySet)
{
    return string.Format(
        CultureInfo.InvariantCulture,
        "{0} virtual DbSet\<{1}> {2} {{ get; set; }}",
        Accessibility.ForReadOnlyProperty(entitySet),
        _typeMapper.GetTypeName(entitySet.ElementType),
        _code.Escape(entitySet));
}
```  

## <a name="service-to-be-tested"></a>要測試服務  

為了示範使用記憶體中測試替身，我們將會撰寫 BlogService 幾個測試的測試。 服務是能夠建立新的部落格 (AddBlog)，並傳回所有部落格會依名稱 (GetAllBlogs)。 除了 GetAllBlogs，我們也提供的方法，將會以非同步方式取得依名稱 (GetAllBlogsAsync) 的所有部落格。  

``` csharp
using System.Collections.Generic;
using System.Data.Entity;
using System.Linq;
using System.Threading.Tasks;

namespace TestingDemo
{
    public class BlogService
    {
        private BloggingContext _context;

        public BlogService(BloggingContext context)
        {
            _context = context;
        }

        public Blog AddBlog(string name, string url)
        {
            var blog = _context.Blogs.Add(new Blog { Name = name, Url = url });
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

## <a name="testing-non-query-scenarios"></a>測試非查詢案例  

這是我們只需要啟動測試非查詢方法。 下列測試使用 Moq 建立的內容。 然後它會建立 DbSet\<部落格\>，並將傳回從該內容的部落格屬性。 接下來，內容用來建立新的 BlogService 然後用來建立新的部落格 – 使用 AddBlog 方法。 最後，測試會驗證服務加入新的部落格，並在內容上呼叫 SaveChanges。  

``` csharp
using Microsoft.VisualStudio.TestTools.UnitTesting;
using Moq;
using System.Data.Entity;

namespace TestingDemo
{
    [TestClass]
    public class NonQueryTests
    {
        [TestMethod]
        public void CreateBlog_saves_a_blog_via_context()
        {
            var mockSet = new Mock<DbSet<Blog>>();

            var mockContext = new Mock<BloggingContext>();
            mockContext.Setup(m => m.Blogs).Returns(mockSet.Object);

            var service = new BlogService(mockContext.Object);
            service.AddBlog("ADO.NET Blog", "http://blogs.msdn.com/adonet");

            mockSet.Verify(m => m.Add(It.IsAny<Blog>()), Times.Once());
            mockContext.Verify(m => m.SaveChanges(), Times.Once());
        }
    }
}
```  

## <a name="testing-query-scenarios"></a>測試查詢案例  

為了要能夠針對我們的 DbSet 測試替身執行查詢，我們需要設定的 IQueryable 實作。 第一個步驟是建立一些記憶體中的資料 – 我們會使用清單\<部落格\>。 接下來，我們建立的內容和 DBSet\<部落格\>然後接通 DbSet – IQueryable 實作它們只 linq to Objects 提供者可搭配清單使用委派\<T\>。  

接著，我們可以建立 BlogService，根據我們的測試替身，並確保我們從 GetAllBlogs 回取得的資料依名稱排序。  

``` csharp
using Microsoft.VisualStudio.TestTools.UnitTesting;
using Moq;
using System.Collections.Generic;
using System.Data.Entity;
using System.Linq;

namespace TestingDemo
{
    [TestClass]
    public class QueryTests
    {
        [TestMethod]
        public void GetAllBlogs_orders_by_name()
        {
            var data = new List<Blog>
            {
                new Blog { Name = "BBB" },
                new Blog { Name = "ZZZ" },
                new Blog { Name = "AAA" },
            }.AsQueryable();

            var mockSet = new Mock<DbSet<Blog>>();
            mockSet.As<IQueryable<Blog>>().Setup(m => m.Provider).Returns(data.Provider);
            mockSet.As<IQueryable<Blog>>().Setup(m => m.Expression).Returns(data.Expression);
            mockSet.As<IQueryable<Blog>>().Setup(m => m.ElementType).Returns(data.ElementType);
            mockSet.As<IQueryable<Blog>>().Setup(m => m.GetEnumerator()).Returns(0 => data.GetEnumerator());

            var mockContext = new Mock<BloggingContext>();
            mockContext.Setup(c => c.Blogs).Returns(mockSet.Object);

            var service = new BlogService(mockContext.Object);
            var blogs = service.GetAllBlogs();

            Assert.AreEqual(3, blogs.Count);
            Assert.AreEqual("AAA", blogs[0].Name);
            Assert.AreEqual("BBB", blogs[1].Name);
            Assert.AreEqual("ZZZ", blogs[2].Name);
        }
    }
}
```  

### <a name="testing-with-async-queries"></a>測試非同步查詢

Entity Framework 6 引進了一組延伸方法，可用來以非同步方式執行查詢。 這些方法的範例包括 ToListAsync、 FirstAsync、 ForEachAsync 等等。  

因為 Entity Framework 查詢會使用 LINQ，IQueryable 和 IEnumerable 上定義的擴充方法。 不過，因為它們只設計來搭配 Entity Framework 您可能會收到下列錯誤，如果您嘗試在 LINQ 查詢無法在 Entity Framework 查詢上使用這些：

> 來源 IQueryable 不實作 IDbAsyncEnumerable{0}。 實作 IDbAsyncEnumerable 的來源可以用於 Entity Framework 的非同步作業。 如需詳細資訊，請參閱[ http://go.microsoft.com/fwlink/?LinkId=287068 ](http://go.microsoft.com/fwlink/?LinkId=287068)。  

儘管對 EF 查詢執行時，才會支援非同步方法，您可能想要針對記憶體中執行測試的雙精度浮點數的 DbSet 時在您的單元測試中使用它們。  

若要使用非同步方法中，我們需要建立的記憶體中 DbAsyncQueryProvider 處理非同步查詢。 儘管有可能設定使用 Moq 查詢提供者，它是在程式碼中建立測試的雙精度浮點實作更為容易。 此實作的程式碼如下所示：  

``` csharp
using System.Collections.Generic;
using System.Data.Entity.Infrastructure;
using System.Linq;
using System.Linq.Expressions;
using System.Threading;
using System.Threading.Tasks;

namespace TestingDemo
{
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

既然我們已經擁有非同步查詢提供者我們可以為我們的新 GetAllBlogsAsync 方法來撰寫單元測試。  

``` csharp
using Microsoft.VisualStudio.TestTools.UnitTesting;
using Moq;
using System.Collections.Generic;
using System.Data.Entity;
using System.Data.Entity.Infrastructure;
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

            var data = new List<Blog>
            {
                new Blog { Name = "BBB" },
                new Blog { Name = "ZZZ" },
                new Blog { Name = "AAA" },
            }.AsQueryable();

            var mockSet = new Mock<DbSet<Blog>>();
            mockSet.As<IDbAsyncEnumerable<Blog>>()
                .Setup(m => m.GetAsyncEnumerator())
                .Returns(new TestDbAsyncEnumerator<Blog>(data.GetEnumerator()));

            mockSet.As<IQueryable<Blog>>()
                .Setup(m => m.Provider)
                .Returns(new TestDbAsyncQueryProvider<Blog>(data.Provider));

            mockSet.As<IQueryable<Blog>>().Setup(m => m.Expression).Returns(data.Expression);
            mockSet.As<IQueryable<Blog>>().Setup(m => m.ElementType).Returns(data.ElementType);
            mockSet.As<IQueryable<Blog>>().Setup(m => m.GetEnumerator()).Returns(data.GetEnumerator());

            var mockContext = new Mock<BloggingContext>();
            mockContext.Setup(c => c.Blogs).Returns(mockSet.Object);

            var service = new BlogService(mockContext.Object);
            var blogs = await service.GetAllBlogsAsync();

            Assert.AreEqual(3, blogs.Count);
            Assert.AreEqual("AAA", blogs[0].Name);
            Assert.AreEqual("BBB", blogs[1].Name);
            Assert.AreEqual("ZZZ", blogs[2].Name);
        }
    }
}
```  
