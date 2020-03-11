---
title: 使用模擬架構進行測試-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: bd66a638-d245-44d4-8e71-b9c6cb335cc7
ms.openlocfilehash: 790e077c5b30c4a68a96b3c1a99b40893b2bbe55
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78419343"
---
# <a name="testing-with-a-mocking-framework"></a><span data-ttu-id="5bc87-102">使用模擬架構進行測試</span><span class="sxs-lookup"><span data-stu-id="5bc87-102">Testing with a mocking framework</span></span>
> [!NOTE]
> <span data-ttu-id="5bc87-103">**僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。</span><span class="sxs-lookup"><span data-stu-id="5bc87-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="5bc87-104">如果您使用的是較早版本，則不適用部分或全部的資訊。</span><span class="sxs-lookup"><span data-stu-id="5bc87-104">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="5bc87-105">撰寫應用程式的測試時，通常會想要避免資料庫的叫用。</span><span class="sxs-lookup"><span data-stu-id="5bc87-105">When writing tests for your application it is often desirable to avoid hitting the database.</span></span>  <span data-ttu-id="5bc87-106">Entity Framework 可讓您藉由建立內容來達成此目的，其中包含測試所定義的行為–這會使用記憶體中的資料。</span><span class="sxs-lookup"><span data-stu-id="5bc87-106">Entity Framework allows you to achieve this by creating a context – with behavior defined by your tests – that makes use of in-memory data.</span></span>  

## <a name="options-for-creating-test-doubles"></a><span data-ttu-id="5bc87-107">建立測試雙精度浮點數的選項</span><span class="sxs-lookup"><span data-stu-id="5bc87-107">Options for creating test doubles</span></span>  

<span data-ttu-id="5bc87-108">有兩種不同的方法可以用來建立內容的記憶體中版本。</span><span class="sxs-lookup"><span data-stu-id="5bc87-108">There are two different approaches that can be used to create an in-memory version of your context.</span></span>  

- <span data-ttu-id="5bc87-109">**建立您自己的測試雙精度**浮點數–此方法牽涉到撰寫您自己在記憶體中的內容和 DbSets 的執行。</span><span class="sxs-lookup"><span data-stu-id="5bc87-109">**Create your own test doubles** – This approach involves writing your own in-memory implementation of your context and DbSets.</span></span> <span data-ttu-id="5bc87-110">這讓您有很大的控制權可以控制類別的行為，但可能牽涉到撰寫和擁有合理的程式碼數量。</span><span class="sxs-lookup"><span data-stu-id="5bc87-110">This gives you a lot of control over how the classes behave but can involve writing and owning a reasonable amount of code.</span></span>  
- <span data-ttu-id="5bc87-111">**使用模擬架構來建立測試加倍**–使用模擬架構（例如 Moq），您可以擁有內容的記憶體內部並在執行時間動態建立的集合。</span><span class="sxs-lookup"><span data-stu-id="5bc87-111">**Use a mocking framework to create test doubles** – Using a mocking framework (such as Moq) you can have the in-memory implementations of your context and sets created dynamically at runtime for you.</span></span>  

<span data-ttu-id="5bc87-112">本文將探討如何使用模擬架構。</span><span class="sxs-lookup"><span data-stu-id="5bc87-112">This article will deal with using a mocking framework.</span></span> <span data-ttu-id="5bc87-113">若要建立您自己的測試，請參閱[使用您自己的測試進行測試的雙](writing-test-doubles.md)精確度。</span><span class="sxs-lookup"><span data-stu-id="5bc87-113">For creating your own test doubles see [Testing with Your Own Test Doubles](writing-test-doubles.md).</span></span>  

<span data-ttu-id="5bc87-114">為了示範如何搭配使用 EF 與模擬架構，我們將使用 Moq。</span><span class="sxs-lookup"><span data-stu-id="5bc87-114">To demonstrate using EF with a mocking framework we are going to use Moq.</span></span> <span data-ttu-id="5bc87-115">取得 Moq 最簡單的方式，就是[從 NuGet 安裝 Moq 套件](https://nuget.org/packages/Moq/)。</span><span class="sxs-lookup"><span data-stu-id="5bc87-115">The easiest way to get Moq is to install the [Moq package from NuGet](https://nuget.org/packages/Moq/).</span></span>  

## <a name="testing-with-pre-ef6-versions"></a><span data-ttu-id="5bc87-116">使用預先 EF6 的版本進行測試</span><span class="sxs-lookup"><span data-stu-id="5bc87-116">Testing with pre-EF6 versions</span></span>  

<span data-ttu-id="5bc87-117">本文所顯示的案例取決於我們在 EF6 中所做的 DbSet 變更。</span><span class="sxs-lookup"><span data-stu-id="5bc87-117">The scenario shown in this article is dependent on some changes we made to DbSet in EF6.</span></span> <span data-ttu-id="5bc87-118">若要使用 EF5 和較舊版本進行測試，請參閱[使用假的內容進行測試](https://romiller.com/2012/02/14/testing-with-a-fake-dbcontext/)。</span><span class="sxs-lookup"><span data-stu-id="5bc87-118">For testing with EF5 and earlier version see [Testing with a Fake Context](https://romiller.com/2012/02/14/testing-with-a-fake-dbcontext/).</span></span>  

## <a name="limitations-of-ef-in-memory-test-doubles"></a><span data-ttu-id="5bc87-119">EF 記憶體內部測試的限制雙精度浮點數</span><span class="sxs-lookup"><span data-stu-id="5bc87-119">Limitations of EF in-memory test doubles</span></span>  

<span data-ttu-id="5bc87-120">記憶體內部測試雙精度浮點數，是提供使用 EF 之應用程式位的單元測試層級涵蓋範圍的好方法。</span><span class="sxs-lookup"><span data-stu-id="5bc87-120">In-memory test doubles can be a good way to provide unit test level coverage of bits of your application that use EF.</span></span> <span data-ttu-id="5bc87-121">不過，在這麼做時，您會使用 LINQ to Objects 對記憶體中的資料執行查詢。</span><span class="sxs-lookup"><span data-stu-id="5bc87-121">However, when doing this you are using LINQ to Objects to execute queries against in-memory data.</span></span> <span data-ttu-id="5bc87-122">這可能會導致不同的行為，而不是使用 EF 的 LINQ 提供者（LINQ to Entities）將查詢轉譯為針對您的資料庫執行的 SQL。</span><span class="sxs-lookup"><span data-stu-id="5bc87-122">This can result in different behavior than using EF’s LINQ provider (LINQ to Entities) to translate queries into SQL that is run against your database.</span></span>  

<span data-ttu-id="5bc87-123">載入相關資料的其中一個範例是這類差異。</span><span class="sxs-lookup"><span data-stu-id="5bc87-123">One example of such a difference is loading related data.</span></span> <span data-ttu-id="5bc87-124">如果您建立一系列的 Blog，其中每個都有相關的文章，則在使用記憶體內部資料時，一律會針對每個 Blog 載入相關的文章。</span><span class="sxs-lookup"><span data-stu-id="5bc87-124">If you create a series of Blogs that each have related Posts, then when using in-memory data the related Posts will always be loaded for each Blog.</span></span> <span data-ttu-id="5bc87-125">不過，針對資料庫執行時，只有在您使用 Include 方法時，才會載入資料。</span><span class="sxs-lookup"><span data-stu-id="5bc87-125">However, when running against a database the data will only be loaded if you use the Include method.</span></span>  

<span data-ttu-id="5bc87-126">基於這個理由，建議一律包含某種層級的端對端測試（除了您的單元測試以外），以確保您的應用程式能針對資料庫正確運作。</span><span class="sxs-lookup"><span data-stu-id="5bc87-126">For this reason, it is recommended to always include some level of end-to-end testing (in addition to your unit tests) to ensure your application works correctly against a database.</span></span>  

## <a name="following-along-with-this-article"></a><span data-ttu-id="5bc87-127">遵循本文</span><span class="sxs-lookup"><span data-stu-id="5bc87-127">Following along with this article</span></span>  

<span data-ttu-id="5bc87-128">本文提供完整的程式代碼清單，您可以將其複製到 Visual Studio，以依照您的需要進行。</span><span class="sxs-lookup"><span data-stu-id="5bc87-128">This article gives complete code listings that you can copy into Visual Studio to follow along if you wish.</span></span> <span data-ttu-id="5bc87-129">最簡單的方式是建立**單元測試專案**，而您將需要以 **.NET Framework 4.5**為目標，才能完成使用 async 的區段。</span><span class="sxs-lookup"><span data-stu-id="5bc87-129">It's easiest to create a **Unit Test Project** and you will need to target **.NET Framework 4.5** to complete the sections that use async.</span></span>  

## <a name="the-ef-model"></a><span data-ttu-id="5bc87-130">EF 模型</span><span class="sxs-lookup"><span data-stu-id="5bc87-130">The EF model</span></span>  

<span data-ttu-id="5bc87-131">我們即將測試的服務會使用由 BloggingCoNtext 和 Blog 和 Post 類別所組成的 EF 模型。</span><span class="sxs-lookup"><span data-stu-id="5bc87-131">The service we're going to test makes use of an EF model made up of the BloggingContext and the Blog and Post classes.</span></span> <span data-ttu-id="5bc87-132">這個程式碼可能是由 EF 設計工具產生，或是 Code First 模型。</span><span class="sxs-lookup"><span data-stu-id="5bc87-132">This code may have been generated by the EF Designer or be a Code First model.</span></span>  

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

### <a name="virtual-dbset-properties-with-ef-designer"></a><span data-ttu-id="5bc87-133">使用 EF 設計工具的虛擬 DbSet 屬性</span><span class="sxs-lookup"><span data-stu-id="5bc87-133">Virtual DbSet properties with EF Designer</span></span>  

<span data-ttu-id="5bc87-134">請注意，內容上的 DbSet 屬性會標示為 virtual。</span><span class="sxs-lookup"><span data-stu-id="5bc87-134">Note that the DbSet properties on the context are marked as virtual.</span></span> <span data-ttu-id="5bc87-135">這可讓模擬架構衍生自我們的內容，並使用模擬的實來覆寫這些屬性。</span><span class="sxs-lookup"><span data-stu-id="5bc87-135">This will allow the mocking framework to derive from our context and overriding these properties with a mocked implementation.</span></span>  

<span data-ttu-id="5bc87-136">如果您使用 Code First 則可以直接編輯您的類別。</span><span class="sxs-lookup"><span data-stu-id="5bc87-136">If you are using Code First then you can edit your classes directly.</span></span> <span data-ttu-id="5bc87-137">如果您使用 EF 設計工具，則必須編輯產生內容的 T4 範本。</span><span class="sxs-lookup"><span data-stu-id="5bc87-137">If you are using the EF Designer then you’ll need to edit the T4 template that generates your context.</span></span> <span data-ttu-id="5bc87-138">開啟 \<model_name\>。在您的 edmx 檔案底下的 CoNtext.tt 檔案，尋找下列程式碼片段，並加入虛擬關鍵字，如下所示。</span><span class="sxs-lookup"><span data-stu-id="5bc87-138">Open up the \<model_name\>.Context.tt file that is nested under you edmx file, find the following fragment of code and add in the virtual keyword as shown.</span></span>  

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

## <a name="service-to-be-tested"></a><span data-ttu-id="5bc87-139">要測試的服務</span><span class="sxs-lookup"><span data-stu-id="5bc87-139">Service to be tested</span></span>  

<span data-ttu-id="5bc87-140">為了示範使用記憶體內部測試進行測試，我們將針對 BlogService 撰寫幾項測試。</span><span class="sxs-lookup"><span data-stu-id="5bc87-140">To demonstrate testing with in-memory test doubles we are going to be writing a couple of tests for a BlogService.</span></span> <span data-ttu-id="5bc87-141">服務能夠建立新的 blog （AddBlog），並傳回依名稱排序的所有 Blog （GetAllBlogs）。</span><span class="sxs-lookup"><span data-stu-id="5bc87-141">The service is capable of creating new blogs (AddBlog) and returning all Blogs ordered by name (GetAllBlogs).</span></span> <span data-ttu-id="5bc87-142">除了 GetAllBlogs 之外，我們也提供了一種方法，可讓您以非同步方式取得依名稱排序的所有 blog （GetAllBlogsAsync）。</span><span class="sxs-lookup"><span data-stu-id="5bc87-142">In addition to GetAllBlogs, we’ve also provided a method that will asynchronously get all blogs ordered by name (GetAllBlogsAsync).</span></span>  

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

## <a name="testing-non-query-scenarios"></a><span data-ttu-id="5bc87-143">測試非查詢案例</span><span class="sxs-lookup"><span data-stu-id="5bc87-143">Testing non-query scenarios</span></span>  

<span data-ttu-id="5bc87-144">這就是開始測試非查詢方法所需執行的動作。</span><span class="sxs-lookup"><span data-stu-id="5bc87-144">That’s all we need to do to start testing non-query methods.</span></span> <span data-ttu-id="5bc87-145">下列測試會使用 Moq 來建立內容。</span><span class="sxs-lookup"><span data-stu-id="5bc87-145">The following test uses Moq to create a context.</span></span> <span data-ttu-id="5bc87-146">接著，它會建立一個 DbSet\<Blog\>，並將它從內容的 Blog 屬性中傳回。</span><span class="sxs-lookup"><span data-stu-id="5bc87-146">It then creates a DbSet\<Blog\> and wires it up to be returned from the context’s Blogs property.</span></span> <span data-ttu-id="5bc87-147">接下來，內容是用來建立新的 BlogService，然後使用 AddBlog 方法建立新的 blog。</span><span class="sxs-lookup"><span data-stu-id="5bc87-147">Next, the context is used to create a new BlogService which is then used to create a new blog – using the AddBlog method.</span></span> <span data-ttu-id="5bc87-148">最後，此測試會驗證服務是否已新增新的 Blog，並在內容上呼叫 SaveChanges。</span><span class="sxs-lookup"><span data-stu-id="5bc87-148">Finally, the test verifies that the service added a new Blog and called SaveChanges on the context.</span></span>  

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

## <a name="testing-query-scenarios"></a><span data-ttu-id="5bc87-149">測試查詢案例</span><span class="sxs-lookup"><span data-stu-id="5bc87-149">Testing query scenarios</span></span>  

<span data-ttu-id="5bc87-150">為了能夠對我們的 DbSet 測試執行查詢，我們需要設定 IQueryable 的執行。</span><span class="sxs-lookup"><span data-stu-id="5bc87-150">In order to be able to execute queries against our DbSet test double we need to setup an implementation of IQueryable.</span></span> <span data-ttu-id="5bc87-151">第一個步驟是建立一些記憶體中的資料，我們使用的是\<Blog\>的清單。</span><span class="sxs-lookup"><span data-stu-id="5bc87-151">The first step is to create some in-memory data – we’re using a List\<Blog\>.</span></span> <span data-ttu-id="5bc87-152">接下來，我們會建立內容並 DBSet\<的 Blog，\> 然後連線到 DbSet 的 IQueryable 執行，它們只會委派給與清單\<T\>搭配運作的 LINQ to Objects 提供者。</span><span class="sxs-lookup"><span data-stu-id="5bc87-152">Next, we create a context and DBSet\<Blog\> then wire up the IQueryable implementation for the DbSet – they’re just delegating to the LINQ to Objects provider that works with List\<T\>.</span></span>  

<span data-ttu-id="5bc87-153">然後，我們可以根據測試加倍來建立 BlogService，並確保從 GetAllBlogs 取回的資料會依名稱排序。</span><span class="sxs-lookup"><span data-stu-id="5bc87-153">We can then create a BlogService based on our test doubles and ensure that the data we get back from GetAllBlogs is ordered by name.</span></span>  

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

### <a name="testing-with-async-queries"></a><span data-ttu-id="5bc87-154">使用非同步查詢進行測試</span><span class="sxs-lookup"><span data-stu-id="5bc87-154">Testing with async queries</span></span>

<span data-ttu-id="5bc87-155">Entity Framework 6 引進了一組擴充方法，可用來以非同步方式執行查詢。</span><span class="sxs-lookup"><span data-stu-id="5bc87-155">Entity Framework 6 introduced a set of extension methods that can be used to asynchronously execute a query.</span></span> <span data-ttu-id="5bc87-156">這些方法的範例包括 ToListAsync、FirstAsync、ForEachAsync 等等。</span><span class="sxs-lookup"><span data-stu-id="5bc87-156">Examples of these methods include ToListAsync, FirstAsync, ForEachAsync, etc.</span></span>  

<span data-ttu-id="5bc87-157">由於 Entity Framework 查詢會利用 LINQ，因此擴充方法會定義于 IQueryable 和 IEnumerable 上。</span><span class="sxs-lookup"><span data-stu-id="5bc87-157">Because Entity Framework queries make use of LINQ, the extension methods are defined on IQueryable and IEnumerable.</span></span> <span data-ttu-id="5bc87-158">不過，因為它們僅設計用於 Entity Framework，如果您嘗試在不是 Entity Framework 查詢的 LINQ 查詢上使用它們，您可能會收到下列錯誤：</span><span class="sxs-lookup"><span data-stu-id="5bc87-158">However, because they are only designed to be used with Entity Framework you may receive the following error if you try to use them on a LINQ query that isn’t an Entity Framework query:</span></span>

> <span data-ttu-id="5bc87-159">來源 IQueryable 不會執行 IDbAsyncEnumerable{0}。</span><span class="sxs-lookup"><span data-stu-id="5bc87-159">The source IQueryable doesn't implement IDbAsyncEnumerable{0}.</span></span> <span data-ttu-id="5bc87-160">只有執行 IDbAsyncEnumerable 的來源可以用於 Entity Framework 非同步作業。</span><span class="sxs-lookup"><span data-stu-id="5bc87-160">Only sources that implement IDbAsyncEnumerable can be used for Entity Framework asynchronous operations.</span></span> <span data-ttu-id="5bc87-161">如需詳細資訊，請參閱[http://go.microsoft.com/fwlink/?LinkId=287068](https://go.microsoft.com/fwlink/?LinkId=287068)。</span><span class="sxs-lookup"><span data-stu-id="5bc87-161">For more details see [http://go.microsoft.com/fwlink/?LinkId=287068](https://go.microsoft.com/fwlink/?LinkId=287068).</span></span>  

<span data-ttu-id="5bc87-162">雖然非同步方法僅在針對 EF 查詢執行時才受到支援，但在對 DbSet 的記憶體中測試雙精度執行時，您可能會想要在單元測試中使用它們。</span><span class="sxs-lookup"><span data-stu-id="5bc87-162">Whilst the async methods are only supported when running against an EF query, you may want to use them in your unit test when running against an in-memory test double of a DbSet.</span></span>  

<span data-ttu-id="5bc87-163">為了使用非同步方法，我們必須建立記憶體內部 DbAsyncQueryProvider 來處理非同步查詢。</span><span class="sxs-lookup"><span data-stu-id="5bc87-163">In order to use the async methods we need to create an in-memory DbAsyncQueryProvider to process the async query.</span></span> <span data-ttu-id="5bc87-164">雖然您可以使用 Moq 來設定查詢提供者，但在程式碼中建立測試雙重執行會比較容易。</span><span class="sxs-lookup"><span data-stu-id="5bc87-164">Whilst it would be possible to setup a query provider using Moq, it is much easier to create a test double implementation in code.</span></span> <span data-ttu-id="5bc87-165">這種執行的程式碼如下所示：</span><span class="sxs-lookup"><span data-stu-id="5bc87-165">The code for this implementation is as follows:</span></span>  

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

<span data-ttu-id="5bc87-166">既然我們已經有非同步查詢提供者，我們就可以為新的 GetAllBlogsAsync 方法撰寫單元測試。</span><span class="sxs-lookup"><span data-stu-id="5bc87-166">Now that we have an async query provider we can write a unit test for our new GetAllBlogsAsync method.</span></span>  

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
