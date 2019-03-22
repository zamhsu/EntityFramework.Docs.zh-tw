---
title: 測試的模擬架構-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: bd66a638-d245-44d4-8e71-b9c6cb335cc7
ms.openlocfilehash: 3d39b41018beb70b72105dfb2fe4d61afc0b0525
ms.sourcegitcommit: eb8359b7ab3b0a1a08522faf67b703a00ecdcefd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/21/2019
ms.locfileid: "58319201"
---
# <a name="testing-with-a-mocking-framework"></a><span data-ttu-id="31390-102">測試的模擬架構</span><span class="sxs-lookup"><span data-stu-id="31390-102">Testing with a mocking framework</span></span>
> [!NOTE]
> <span data-ttu-id="31390-103">**僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。</span><span class="sxs-lookup"><span data-stu-id="31390-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="31390-104">如果您使用的是較早版本，則不適用部分或全部的資訊。</span><span class="sxs-lookup"><span data-stu-id="31390-104">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="31390-105">撰寫您的應用程式的測試時，通常最好避免達到資料庫。</span><span class="sxs-lookup"><span data-stu-id="31390-105">When writing tests for your application it is often desirable to avoid hitting the database.</span></span>  <span data-ttu-id="31390-106">Entity Framework 可讓您達成此目的所建立的內容 – 定義您的測試-的行為，以便使用的記憶體中的資料。</span><span class="sxs-lookup"><span data-stu-id="31390-106">Entity Framework allows you to achieve this by creating a context – with behavior defined by your tests – that makes use of in-memory data.</span></span>  

## <a name="options-for-creating-test-doubles"></a><span data-ttu-id="31390-107">建立測試替身的選項</span><span class="sxs-lookup"><span data-stu-id="31390-107">Options for creating test doubles</span></span>  

<span data-ttu-id="31390-108">有兩種不同的方法，可用來建立您的內容是記憶體中的舊版。</span><span class="sxs-lookup"><span data-stu-id="31390-108">There are two different approaches that can be used to create an in-memory version of your context.</span></span>  

- <span data-ttu-id="31390-109">**建立您自己的測試替身**– 這種方法涉及撰寫您自己的記憶體中實作您的內容和 DbSets。</span><span class="sxs-lookup"><span data-stu-id="31390-109">**Create your own test doubles** – This approach involves writing your own in-memory implementation of your context and DbSets.</span></span> <span data-ttu-id="31390-110">這可讓您控制類別如何運作，但是可以寫入和擁有合理的程式碼牽涉到很多。</span><span class="sxs-lookup"><span data-stu-id="31390-110">This gives you a lot of control over how the classes behave but can involve writing and owning a reasonable amount of code.</span></span>  
- <span data-ttu-id="31390-111">**使用模擬的架構來建立測試替身**– 使用模擬的架構 （例如 Moq) 您可以讓您的內容和設定在執行階段以動態方式建立您的記憶體中實作。</span><span class="sxs-lookup"><span data-stu-id="31390-111">**Use a mocking framework to create test doubles** – Using a mocking framework (such as Moq) you can have the in-memory implementations of your context and sets created dynamically at runtime for you.</span></span>  

<span data-ttu-id="31390-112">這篇文章將會處理使用的模擬架構。</span><span class="sxs-lookup"><span data-stu-id="31390-112">This article will deal with using a mocking framework.</span></span> <span data-ttu-id="31390-113">建立您自己的測試替身請參閱[測試您自己的測試替身](writing-test-doubles.md)。</span><span class="sxs-lookup"><span data-stu-id="31390-113">For creating your own test doubles see [Testing with Your Own Test Doubles](writing-test-doubles.md).</span></span>  

<span data-ttu-id="31390-114">我們要使用 Moq 來示範如何使用 EF 使用的模擬架構。</span><span class="sxs-lookup"><span data-stu-id="31390-114">To demonstrate using EF with a mocking framework we are going to use Moq.</span></span> <span data-ttu-id="31390-115">若要取得 Moq 最簡單的方式是安裝[Moq 封裝從 NuGet](http://nuget.org/packages/Moq/)。</span><span class="sxs-lookup"><span data-stu-id="31390-115">The easiest way to get Moq is to install the [Moq package from NuGet](http://nuget.org/packages/Moq/).</span></span>  

## <a name="testing-with-pre-ef6-versions"></a><span data-ttu-id="31390-116">測試前 EF6 版本</span><span class="sxs-lookup"><span data-stu-id="31390-116">Testing with pre-EF6 versions</span></span>  

<span data-ttu-id="31390-117">這篇文章中所示的案例是依據我們對 DbSet 在 EF6 中進行一些變更。</span><span class="sxs-lookup"><span data-stu-id="31390-117">The scenario shown in this article is dependent on some changes we made to DbSet in EF6.</span></span> <span data-ttu-id="31390-118">使用 EF5 和更舊版本進行測試，請參閱[測試與假的內容](http://romiller.com/2012/02/14/testing-with-a-fake-dbcontext/)。</span><span class="sxs-lookup"><span data-stu-id="31390-118">For testing with EF5 and earlier version see [Testing with a Fake Context](http://romiller.com/2012/02/14/testing-with-a-fake-dbcontext/).</span></span>  

## <a name="limitations-of-ef-in-memory-test-doubles"></a><span data-ttu-id="31390-119">EF 記憶體測試替身的限制</span><span class="sxs-lookup"><span data-stu-id="31390-119">Limitations of EF in-memory test doubles</span></span>  

<span data-ttu-id="31390-120">記憶體中的測試替身可提供單元測試的應用程式使用 EF 的位元層級的涵蓋範圍的好方法。</span><span class="sxs-lookup"><span data-stu-id="31390-120">In-memory test doubles can be a good way to provide unit test level coverage of bits of your application that use EF.</span></span> <span data-ttu-id="31390-121">不過，這麼做時您會使用 LINQ to Objects 來針對記憶體中的資料執行查詢。</span><span class="sxs-lookup"><span data-stu-id="31390-121">However, when doing this you are using LINQ to Objects to execute queries against in-memory data.</span></span> <span data-ttu-id="31390-122">這可能會導致不同的行為，比使用 EF 的 LINQ 提供者 (LINQ to Entities)，將查詢轉譯為針對您的資料庫執行的 SQL。</span><span class="sxs-lookup"><span data-stu-id="31390-122">This can result in different behavior than using EF’s LINQ provider (LINQ to Entities) to translate queries into SQL that is run against your database.</span></span>  

<span data-ttu-id="31390-123">這類差異的其中一個範例會載入相關的資料。</span><span class="sxs-lookup"><span data-stu-id="31390-123">One example of such a difference is loading related data.</span></span> <span data-ttu-id="31390-124">如果您建立一系列部落格的每個有相關的文章，然後使用記憶體中的資料時的相關的文章一律要載入的每個部落格。</span><span class="sxs-lookup"><span data-stu-id="31390-124">If you create a series of Blogs that each have related Posts, then when using in-memory data the related Posts will always be loaded for each Blog.</span></span> <span data-ttu-id="31390-125">不過，對資料庫執行時載入的資料將僅如果您使用 Include 方法。</span><span class="sxs-lookup"><span data-stu-id="31390-125">However, when running against a database the data will only be loaded if you use the Include method.</span></span>  

<span data-ttu-id="31390-126">基於這個理由，建議一律包含某種程度的端對端測試 若要確保您的應用程式運作正確地針對資料庫的 電子郵件 （除了您的單元測試）。</span><span class="sxs-lookup"><span data-stu-id="31390-126">For this reason, it is recommended to always include some level of end-to-end testing (in addition to your unit tests) to ensure your application works correctly against a database.</span></span>  

## <a name="following-along-with-this-article"></a><span data-ttu-id="31390-127">觀摩這份文件</span><span class="sxs-lookup"><span data-stu-id="31390-127">Following along with this article</span></span>  

<span data-ttu-id="31390-128">這篇文章可讓您可以將它複製到要跟著做如果您想要的 Visual Studio 的完整程式碼清單。</span><span class="sxs-lookup"><span data-stu-id="31390-128">This article gives complete code listings that you can copy into Visual Studio to follow along if you wish.</span></span> <span data-ttu-id="31390-129">它是最簡單的方式建立**單元測試專案**，您必須到目標 **.NET Framework 4.5**完成使用非同步的區段。</span><span class="sxs-lookup"><span data-stu-id="31390-129">It's easiest to create a **Unit Test Project** and you will need to target **.NET Framework 4.5** to complete the sections that use async.</span></span>  

## <a name="the-ef-model"></a><span data-ttu-id="31390-130">EF 模型</span><span class="sxs-lookup"><span data-stu-id="31390-130">The EF model</span></span>  

<span data-ttu-id="31390-131">我們要測試此服務可讓您可使用的 EF 模型為 [bloggingcontext] 和部落格和文章類別組成。</span><span class="sxs-lookup"><span data-stu-id="31390-131">The service we're going to test makes use of an EF model made up of the BloggingContext and the Blog and Post classes.</span></span> <span data-ttu-id="31390-132">此程式碼可能會有 EF 設計工具所產生或 Code First 模型。</span><span class="sxs-lookup"><span data-stu-id="31390-132">This code may have been generated by the EF Designer or be a Code First model.</span></span>  

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

### <a name="virtual-dbset-properties-with-ef-designer"></a><span data-ttu-id="31390-133">使用 EF 設計工具的虛擬 DbSet 屬性</span><span class="sxs-lookup"><span data-stu-id="31390-133">Virtual DbSet properties with EF Designer</span></span>  

<span data-ttu-id="31390-134">請注意，在內容上的 DbSet 屬性會標示為虛擬。</span><span class="sxs-lookup"><span data-stu-id="31390-134">Note that the DbSet properties on the context are marked as virtual.</span></span> <span data-ttu-id="31390-135">這可讓模擬的架構，以衍生自我們的內容，並覆寫這些屬性與模擬 （mock） 實作。</span><span class="sxs-lookup"><span data-stu-id="31390-135">This will allow the mocking framework to derive from our context and overriding these properties with a mocked implementation.</span></span>  

<span data-ttu-id="31390-136">如果您使用 Code First，然後您可以直接編輯您的類別。</span><span class="sxs-lookup"><span data-stu-id="31390-136">If you are using Code First then you can edit your classes directly.</span></span> <span data-ttu-id="31390-137">如果您使用 EF 設計工具將需要編輯 T4 範本產生您的內容。</span><span class="sxs-lookup"><span data-stu-id="31390-137">If you are using the EF Designer then you’ll need to edit the T4 template that generates your context.</span></span> <span data-ttu-id="31390-138">開啟\<model_name\>。Context.tt 檔案底下您 edmx 檔案，尋找下列程式碼片段，並新增虛擬關鍵字中所示。</span><span class="sxs-lookup"><span data-stu-id="31390-138">Open up the \<model_name\>.Context.tt file that is nested under you edmx file, find the following fragment of code and add in the virtual keyword as shown.</span></span>  

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

## <a name="service-to-be-tested"></a><span data-ttu-id="31390-139">要測試服務</span><span class="sxs-lookup"><span data-stu-id="31390-139">Service to be tested</span></span>  

<span data-ttu-id="31390-140">為了示範使用記憶體中測試替身，我們將會撰寫 BlogService 幾個測試的測試。</span><span class="sxs-lookup"><span data-stu-id="31390-140">To demonstrate testing with in-memory test doubles we are going to be writing a couple of tests for a BlogService.</span></span> <span data-ttu-id="31390-141">服務是能夠建立新的部落格 (AddBlog)，並傳回所有部落格會依名稱 (GetAllBlogs)。</span><span class="sxs-lookup"><span data-stu-id="31390-141">The service is capable of creating new blogs (AddBlog) and returning all Blogs ordered by name (GetAllBlogs).</span></span> <span data-ttu-id="31390-142">除了 GetAllBlogs，我們也提供的方法，將會以非同步方式取得依名稱 (GetAllBlogsAsync) 的所有部落格。</span><span class="sxs-lookup"><span data-stu-id="31390-142">In addition to GetAllBlogs, we’ve also provided a method that will asynchronously get all blogs ordered by name (GetAllBlogsAsync).</span></span>  

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

## <a name="testing-non-query-scenarios"></a><span data-ttu-id="31390-143">測試非查詢案例</span><span class="sxs-lookup"><span data-stu-id="31390-143">Testing non-query scenarios</span></span>  

<span data-ttu-id="31390-144">這是我們只需要啟動測試非查詢方法。</span><span class="sxs-lookup"><span data-stu-id="31390-144">That’s all we need to do to start testing non-query methods.</span></span> <span data-ttu-id="31390-145">下列測試使用 Moq 建立的內容。</span><span class="sxs-lookup"><span data-stu-id="31390-145">The following test uses Moq to create a context.</span></span> <span data-ttu-id="31390-146">然後它會建立 DbSet\<部落格\>，並將傳回從該內容的部落格屬性。</span><span class="sxs-lookup"><span data-stu-id="31390-146">It then creates a DbSet\<Blog\> and wires it up to be returned from the context’s Blogs property.</span></span> <span data-ttu-id="31390-147">接下來，內容用來建立新的 BlogService 然後用來建立新的部落格 – 使用 AddBlog 方法。</span><span class="sxs-lookup"><span data-stu-id="31390-147">Next, the context is used to create a new BlogService which is then used to create a new blog – using the AddBlog method.</span></span> <span data-ttu-id="31390-148">最後，測試會驗證服務加入新的部落格，並在內容上呼叫 SaveChanges。</span><span class="sxs-lookup"><span data-stu-id="31390-148">Finally, the test verifies that the service added a new Blog and called SaveChanges on the context.</span></span>  

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

## <a name="testing-query-scenarios"></a><span data-ttu-id="31390-149">測試查詢案例</span><span class="sxs-lookup"><span data-stu-id="31390-149">Testing query scenarios</span></span>  

<span data-ttu-id="31390-150">為了要能夠針對我們的 DbSet 測試替身執行查詢，我們需要設定的 IQueryable 實作。</span><span class="sxs-lookup"><span data-stu-id="31390-150">In order to be able to execute queries against our DbSet test double we need to setup an implementation of IQueryable.</span></span> <span data-ttu-id="31390-151">第一個步驟是建立一些記憶體中的資料 – 我們會使用清單\<部落格\>。</span><span class="sxs-lookup"><span data-stu-id="31390-151">The first step is to create some in-memory data – we’re using a List\<Blog\>.</span></span> <span data-ttu-id="31390-152">接下來，我們建立的內容和 DBSet\<部落格\>然後接通 DbSet – IQueryable 實作它們只 linq to Objects 提供者可搭配清單使用委派\<T\>。</span><span class="sxs-lookup"><span data-stu-id="31390-152">Next, we create a context and DBSet\<Blog\> then wire up the IQueryable implementation for the DbSet – they’re just delegating to the LINQ to Objects provider that works with List\<T\>.</span></span>  

<span data-ttu-id="31390-153">接著，我們可以建立 BlogService，根據我們的測試替身，並確保我們從 GetAllBlogs 回取得的資料依名稱排序。</span><span class="sxs-lookup"><span data-stu-id="31390-153">We can then create a BlogService based on our test doubles and ensure that the data we get back from GetAllBlogs is ordered by name.</span></span>  

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
            mockSet.As<IQueryable<Blog>>().Setup(m => m.GetEnumerator()).Returns(data.GetEnumerator());

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

### <a name="testing-with-async-queries"></a><span data-ttu-id="31390-154">測試非同步查詢</span><span class="sxs-lookup"><span data-stu-id="31390-154">Testing with async queries</span></span>

<span data-ttu-id="31390-155">Entity Framework 6 引進了一組延伸方法，可用來以非同步方式執行查詢。</span><span class="sxs-lookup"><span data-stu-id="31390-155">Entity Framework 6 introduced a set of extension methods that can be used to asynchronously execute a query.</span></span> <span data-ttu-id="31390-156">這些方法的範例包括 ToListAsync、 FirstAsync、 ForEachAsync 等等。</span><span class="sxs-lookup"><span data-stu-id="31390-156">Examples of these methods include ToListAsync, FirstAsync, ForEachAsync, etc.</span></span>  

<span data-ttu-id="31390-157">因為 Entity Framework 查詢會使用 LINQ，IQueryable 和 IEnumerable 上定義的擴充方法。</span><span class="sxs-lookup"><span data-stu-id="31390-157">Because Entity Framework queries make use of LINQ, the extension methods are defined on IQueryable and IEnumerable.</span></span> <span data-ttu-id="31390-158">不過，因為它們只設計來搭配 Entity Framework 您可能會收到下列錯誤，如果您嘗試在 LINQ 查詢無法在 Entity Framework 查詢上使用這些：</span><span class="sxs-lookup"><span data-stu-id="31390-158">However, because they are only designed to be used with Entity Framework you may receive the following error if you try to use them on a LINQ query that isn’t an Entity Framework query:</span></span>

> <span data-ttu-id="31390-159">來源 IQueryable 不實作 IDbAsyncEnumerable{0}。</span><span class="sxs-lookup"><span data-stu-id="31390-159">The source IQueryable doesn't implement IDbAsyncEnumerable{0}.</span></span> <span data-ttu-id="31390-160">實作 IDbAsyncEnumerable 的來源可以用於 Entity Framework 的非同步作業。</span><span class="sxs-lookup"><span data-stu-id="31390-160">Only sources that implement IDbAsyncEnumerable can be used for Entity Framework asynchronous operations.</span></span> <span data-ttu-id="31390-161">如需詳細資訊，請參閱[ http://go.microsoft.com/fwlink/?LinkId=287068 ](https://go.microsoft.com/fwlink/?LinkId=287068)。</span><span class="sxs-lookup"><span data-stu-id="31390-161">For more details see [http://go.microsoft.com/fwlink/?LinkId=287068](https://go.microsoft.com/fwlink/?LinkId=287068).</span></span>  

<span data-ttu-id="31390-162">儘管對 EF 查詢執行時，才會支援非同步方法，您可能想要針對記憶體中執行測試的雙精度浮點數的 DbSet 時在您的單元測試中使用它們。</span><span class="sxs-lookup"><span data-stu-id="31390-162">Whilst the async methods are only supported when running against an EF query, you may want to use them in your unit test when running against an in-memory test double of a DbSet.</span></span>  

<span data-ttu-id="31390-163">若要使用非同步方法中，我們需要建立的記憶體中 DbAsyncQueryProvider 處理非同步查詢。</span><span class="sxs-lookup"><span data-stu-id="31390-163">In order to use the async methods we need to create an in-memory DbAsyncQueryProvider to process the async query.</span></span> <span data-ttu-id="31390-164">儘管有可能設定使用 Moq 查詢提供者，它是在程式碼中建立測試的雙精度浮點實作更為容易。</span><span class="sxs-lookup"><span data-stu-id="31390-164">Whilst it would be possible to setup a query provider using Moq, it is much easier to create a test double implementation in code.</span></span> <span data-ttu-id="31390-165">此實作的程式碼如下所示：</span><span class="sxs-lookup"><span data-stu-id="31390-165">The code for this implementation is as follows:</span></span>  

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

<span data-ttu-id="31390-166">既然我們已經擁有非同步查詢提供者我們可以為我們的新 GetAllBlogsAsync 方法來撰寫單元測試。</span><span class="sxs-lookup"><span data-stu-id="31390-166">Now that we have an async query provider we can write a unit test for our new GetAllBlogsAsync method.</span></span>  

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
