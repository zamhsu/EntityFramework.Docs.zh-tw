---
title: 使用模擬架構進行測試-EF6
description: 在 Entity Framework 6 中使用模擬 framework 進行測試
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/testing/mocking
ms.openlocfilehash: ebd57ca2127bab32c94d806f8213739a6e0b4f0a
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070480"
---
# <a name="testing-with-a-mocking-framework"></a><span data-ttu-id="4e3b6-103">使用模擬架構進行測試</span><span class="sxs-lookup"><span data-stu-id="4e3b6-103">Testing with a mocking framework</span></span>
> [!NOTE]
> <span data-ttu-id="4e3b6-104">**僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。</span><span class="sxs-lookup"><span data-stu-id="4e3b6-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="4e3b6-105">如果您使用的是較早版本，則不適用部分或全部的資訊。</span><span class="sxs-lookup"><span data-stu-id="4e3b6-105">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="4e3b6-106">當您為應用程式撰寫測試時，通常會想要避免叫用資料庫。</span><span class="sxs-lookup"><span data-stu-id="4e3b6-106">When writing tests for your application it is often desirable to avoid hitting the database.</span></span>  <span data-ttu-id="4e3b6-107">Entity Framework 可讓您藉由建立內容（具有您的測試所定義的行為）來達成此目的，以利用記憶體內部資料。</span><span class="sxs-lookup"><span data-stu-id="4e3b6-107">Entity Framework allows you to achieve this by creating a context – with behavior defined by your tests – that makes use of in-memory data.</span></span>  

## <a name="options-for-creating-test-doubles"></a><span data-ttu-id="4e3b6-108">建立測試雙精度浮點數的選項</span><span class="sxs-lookup"><span data-stu-id="4e3b6-108">Options for creating test doubles</span></span>  

<span data-ttu-id="4e3b6-109">有兩種不同的方法可以用來建立內容的記憶體內版本。</span><span class="sxs-lookup"><span data-stu-id="4e3b6-109">There are two different approaches that can be used to create an in-memory version of your context.</span></span>  

- <span data-ttu-id="4e3b6-110">**建立您自己的測試雙精度** 浮點數–此方法牽涉到撰寫您自己的內容和 DbSets 的記憶體內部執行。</span><span class="sxs-lookup"><span data-stu-id="4e3b6-110">**Create your own test doubles** – This approach involves writing your own in-memory implementation of your context and DbSets.</span></span> <span data-ttu-id="4e3b6-111">這可讓您充分掌控類別的行為，但可能牽涉到撰寫和擁有合理的程式碼數量。</span><span class="sxs-lookup"><span data-stu-id="4e3b6-111">This gives you a lot of control over how the classes behave but can involve writing and owning a reasonable amount of code.</span></span>  
- <span data-ttu-id="4e3b6-112">**使用模擬架構來建立測試雙精度** 浮點數（使用模擬架構 (例如) Moq），您可以擁有內容的記憶體內部執行，並在執行時間以動態方式建立的集合。</span><span class="sxs-lookup"><span data-stu-id="4e3b6-112">**Use a mocking framework to create test doubles** – Using a mocking framework (such as Moq) you can have the in-memory implementations of your context and sets created dynamically at runtime for you.</span></span>  

<span data-ttu-id="4e3b6-113">本文將使用模擬架構來處理。</span><span class="sxs-lookup"><span data-stu-id="4e3b6-113">This article will deal with using a mocking framework.</span></span> <span data-ttu-id="4e3b6-114">若要建立您自己的測試，請參閱 [使用自己的測試進行測試](xref:ef6/fundamentals/testing/writing-test-doubles)。</span><span class="sxs-lookup"><span data-stu-id="4e3b6-114">For creating your own test doubles see [Testing with Your Own Test Doubles](xref:ef6/fundamentals/testing/writing-test-doubles).</span></span>  

<span data-ttu-id="4e3b6-115">為了示範如何搭配使用 EF 與模擬架構，我們將使用 Moq。</span><span class="sxs-lookup"><span data-stu-id="4e3b6-115">To demonstrate using EF with a mocking framework we are going to use Moq.</span></span> <span data-ttu-id="4e3b6-116">取得 Moq 最簡單的方式是 [從 NuGet 安裝 Moq 套件](https://nuget.org/packages/Moq/)。</span><span class="sxs-lookup"><span data-stu-id="4e3b6-116">The easiest way to get Moq is to install the [Moq package from NuGet](https://nuget.org/packages/Moq/).</span></span>  

## <a name="testing-with-pre-ef6-versions"></a><span data-ttu-id="4e3b6-117">使用預先 EF6 的版本進行測試</span><span class="sxs-lookup"><span data-stu-id="4e3b6-117">Testing with pre-EF6 versions</span></span>  

<span data-ttu-id="4e3b6-118">本文所示的案例取決於我們在 EF6 中進行 DbSet 的一些變更。</span><span class="sxs-lookup"><span data-stu-id="4e3b6-118">The scenario shown in this article is dependent on some changes we made to DbSet in EF6.</span></span> <span data-ttu-id="4e3b6-119">若要使用 EF5 和更早版本進行測試，請參閱 [使用假內容進行測試](https://romiller.com/2012/02/14/testing-with-a-fake-dbcontext/)。</span><span class="sxs-lookup"><span data-stu-id="4e3b6-119">For testing with EF5 and earlier version see [Testing with a Fake Context](https://romiller.com/2012/02/14/testing-with-a-fake-dbcontext/).</span></span>  

## <a name="limitations-of-ef-in-memory-test-doubles"></a><span data-ttu-id="4e3b6-120">EF in memory 測試雙精度浮點數的限制</span><span class="sxs-lookup"><span data-stu-id="4e3b6-120">Limitations of EF in-memory test doubles</span></span>  

<span data-ttu-id="4e3b6-121">記憶體內部測試雙精度浮點數，可提供應用程式中使用 EF 之位的單元測試層級涵蓋範圍。</span><span class="sxs-lookup"><span data-stu-id="4e3b6-121">In-memory test doubles can be a good way to provide unit test level coverage of bits of your application that use EF.</span></span> <span data-ttu-id="4e3b6-122">但是，當您這麼做時，您會使用 LINQ to Objects 針對記憶體中的資料執行查詢。</span><span class="sxs-lookup"><span data-stu-id="4e3b6-122">However, when doing this you are using LINQ to Objects to execute queries against in-memory data.</span></span> <span data-ttu-id="4e3b6-123">這可能會導致不同的行為，而不是使用 EF 的 LINQ 提供者 (LINQ to Entities) 將查詢轉譯成針對您的資料庫執行的 SQL。</span><span class="sxs-lookup"><span data-stu-id="4e3b6-123">This can result in different behavior than using EF’s LINQ provider (LINQ to Entities) to translate queries into SQL that is run against your database.</span></span>  

<span data-ttu-id="4e3b6-124">這種差異的其中一個範例是載入相關資料。</span><span class="sxs-lookup"><span data-stu-id="4e3b6-124">One example of such a difference is loading related data.</span></span> <span data-ttu-id="4e3b6-125">如果您建立一系列的 Blog，其中每個都有相關的貼文，則在使用記憶體內部資料時，一律會針對每個 Blog 載入相關的文章。</span><span class="sxs-lookup"><span data-stu-id="4e3b6-125">If you create a series of Blogs that each have related Posts, then when using in-memory data the related Posts will always be loaded for each Blog.</span></span> <span data-ttu-id="4e3b6-126">不過，針對資料庫執行時，只有當您使用 Include 方法時，才會載入資料。</span><span class="sxs-lookup"><span data-stu-id="4e3b6-126">However, when running against a database the data will only be loaded if you use the Include method.</span></span>  

<span data-ttu-id="4e3b6-127">基於這個理由，建議您一律包含部分端對端測試 (除了您的單元測試) ，以確保您的應用程式能針對資料庫正確運作。</span><span class="sxs-lookup"><span data-stu-id="4e3b6-127">For this reason, it is recommended to always include some level of end-to-end testing (in addition to your unit tests) to ensure your application works correctly against a database.</span></span>  

## <a name="following-along-with-this-article"></a><span data-ttu-id="4e3b6-128">遵循本文</span><span class="sxs-lookup"><span data-stu-id="4e3b6-128">Following along with this article</span></span>  

<span data-ttu-id="4e3b6-129">本文提供完整的程式代碼清單，可讓您視需要複製到 Visual Studio 以進行追蹤。</span><span class="sxs-lookup"><span data-stu-id="4e3b6-129">This article gives complete code listings that you can copy into Visual Studio to follow along if you wish.</span></span> <span data-ttu-id="4e3b6-130">最簡單的方式是建立 **單元測試專案** ，您必須將 **.NET Framework 4.5** 為目標，才能完成使用 async 的區段。</span><span class="sxs-lookup"><span data-stu-id="4e3b6-130">It's easiest to create a **Unit Test Project** and you will need to target **.NET Framework 4.5** to complete the sections that use async.</span></span>  

## <a name="the-ef-model"></a><span data-ttu-id="4e3b6-131">EF 模型</span><span class="sxs-lookup"><span data-stu-id="4e3b6-131">The EF model</span></span>  

<span data-ttu-id="4e3b6-132">我們即將測試的服務會使用由 BloggingCoNtext 和 Blog 和 Post 類別組成的 EF 模型。</span><span class="sxs-lookup"><span data-stu-id="4e3b6-132">The service we're going to test makes use of an EF model made up of the BloggingContext and the Blog and Post classes.</span></span> <span data-ttu-id="4e3b6-133">這段程式碼可能由 EF 設計工具產生，或為 Code First 模型。</span><span class="sxs-lookup"><span data-stu-id="4e3b6-133">This code may have been generated by the EF Designer or be a Code First model.</span></span>  

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

### <a name="virtual-dbset-properties-with-ef-designer"></a><span data-ttu-id="4e3b6-134">使用 EF 設計工具的虛擬 DbSet 屬性</span><span class="sxs-lookup"><span data-stu-id="4e3b6-134">Virtual DbSet properties with EF Designer</span></span>  

<span data-ttu-id="4e3b6-135">請注意，內容上的 DbSet 屬性會標示為虛擬。</span><span class="sxs-lookup"><span data-stu-id="4e3b6-135">Note that the DbSet properties on the context are marked as virtual.</span></span> <span data-ttu-id="4e3b6-136">這可讓模擬架構衍生自我們的內容，並使用模擬的執行覆寫這些屬性。</span><span class="sxs-lookup"><span data-stu-id="4e3b6-136">This will allow the mocking framework to derive from our context and overriding these properties with a mocked implementation.</span></span>  

<span data-ttu-id="4e3b6-137">如果您使用 Code First 則可以直接編輯類別。</span><span class="sxs-lookup"><span data-stu-id="4e3b6-137">If you are using Code First then you can edit your classes directly.</span></span> <span data-ttu-id="4e3b6-138">如果您使用 EF 設計工具，則必須編輯產生內容的 T4 範本。</span><span class="sxs-lookup"><span data-stu-id="4e3b6-138">If you are using the EF Designer then you’ll need to edit the T4 template that generates your context.</span></span> <span data-ttu-id="4e3b6-139">開啟 \<model_name\> 。內嵌在您 edmx 檔案底下的 CoNtext.tt 檔案，尋找下列程式碼片段，並新增至虛擬關鍵字，如下所示。</span><span class="sxs-lookup"><span data-stu-id="4e3b6-139">Open up the \<model_name\>.Context.tt file that is nested under you edmx file, find the following fragment of code and add in the virtual keyword as shown.</span></span>  

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

## <a name="service-to-be-tested"></a><span data-ttu-id="4e3b6-140">要測試的服務</span><span class="sxs-lookup"><span data-stu-id="4e3b6-140">Service to be tested</span></span>  

<span data-ttu-id="4e3b6-141">為了示範如何使用記憶體內部測試進行測試，我們將為 BlogService 撰寫幾項測試。</span><span class="sxs-lookup"><span data-stu-id="4e3b6-141">To demonstrate testing with in-memory test doubles we are going to be writing a couple of tests for a BlogService.</span></span> <span data-ttu-id="4e3b6-142">服務能夠建立新的 blog (AddBlog) 並傳回依名稱 (GetAllBlogs) 排序的所有 Blog。</span><span class="sxs-lookup"><span data-stu-id="4e3b6-142">The service is capable of creating new blogs (AddBlog) and returning all Blogs ordered by name (GetAllBlogs).</span></span> <span data-ttu-id="4e3b6-143">除了 GetAllBlogs 之外，我們也提供了一種方法，會以非同步方式取得依名稱排序的所有 blog (GetAllBlogsAsync) 。</span><span class="sxs-lookup"><span data-stu-id="4e3b6-143">In addition to GetAllBlogs, we’ve also provided a method that will asynchronously get all blogs ordered by name (GetAllBlogsAsync).</span></span>  

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

## <a name="testing-non-query-scenarios"></a><span data-ttu-id="4e3b6-144">測試非查詢案例</span><span class="sxs-lookup"><span data-stu-id="4e3b6-144">Testing non-query scenarios</span></span>  

<span data-ttu-id="4e3b6-145">這就是開始測試非查詢方法所需的一切。</span><span class="sxs-lookup"><span data-stu-id="4e3b6-145">That’s all we need to do to start testing non-query methods.</span></span> <span data-ttu-id="4e3b6-146">下列測試會使用 Moq 來建立內容。</span><span class="sxs-lookup"><span data-stu-id="4e3b6-146">The following test uses Moq to create a context.</span></span> <span data-ttu-id="4e3b6-147">然後，它會建立 DbSet \<Blog\> ，並將它從內容的 blog 屬性傳回。</span><span class="sxs-lookup"><span data-stu-id="4e3b6-147">It then creates a DbSet\<Blog\> and wires it up to be returned from the context’s Blogs property.</span></span> <span data-ttu-id="4e3b6-148">接下來，此內容會用來建立新的 BlogService，然後用來建立新的 blog （使用 AddBlog 方法）。</span><span class="sxs-lookup"><span data-stu-id="4e3b6-148">Next, the context is used to create a new BlogService which is then used to create a new blog – using the AddBlog method.</span></span> <span data-ttu-id="4e3b6-149">最後，測試會確認服務已在內容上新增新的 Blog 和呼叫 SaveChanges。</span><span class="sxs-lookup"><span data-stu-id="4e3b6-149">Finally, the test verifies that the service added a new Blog and called SaveChanges on the context.</span></span>  

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

## <a name="testing-query-scenarios"></a><span data-ttu-id="4e3b6-150">測試查詢案例</span><span class="sxs-lookup"><span data-stu-id="4e3b6-150">Testing query scenarios</span></span>  

<span data-ttu-id="4e3b6-151">為了能夠對我們的 DbSet 測試執行查詢，我們必須設定 IQueryable 的執行。</span><span class="sxs-lookup"><span data-stu-id="4e3b6-151">In order to be able to execute queries against our DbSet test double we need to setup an implementation of IQueryable.</span></span> <span data-ttu-id="4e3b6-152">第一個步驟是建立一些記憶體內部資料，我們使用的是清單 \<Blog\> 。</span><span class="sxs-lookup"><span data-stu-id="4e3b6-152">The first step is to create some in-memory data – we’re using a List\<Blog\>.</span></span> <span data-ttu-id="4e3b6-153">接下來，我們會建立內容，然後 DBSet \<Blog\> DBSet 的 IQueryable 執行，它們只是委派給搭配 List 的 LINQ to Objects 提供者 \<T\> 。</span><span class="sxs-lookup"><span data-stu-id="4e3b6-153">Next, we create a context and DBSet\<Blog\> then wire up the IQueryable implementation for the DbSet – they’re just delegating to the LINQ to Objects provider that works with List\<T\>.</span></span>  

<span data-ttu-id="4e3b6-154">接著，我們可以根據測試的雙精度浮點數來建立 BlogService，並確保從 GetAllBlogs 取回的資料會依名稱排序。</span><span class="sxs-lookup"><span data-stu-id="4e3b6-154">We can then create a BlogService based on our test doubles and ensure that the data we get back from GetAllBlogs is ordered by name.</span></span>  

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

### <a name="testing-with-async-queries"></a><span data-ttu-id="4e3b6-155">使用非同步查詢進行測試</span><span class="sxs-lookup"><span data-stu-id="4e3b6-155">Testing with async queries</span></span>

<span data-ttu-id="4e3b6-156">Entity Framework 6 引進了一組擴充方法，可用來以非同步方式執行查詢。</span><span class="sxs-lookup"><span data-stu-id="4e3b6-156">Entity Framework 6 introduced a set of extension methods that can be used to asynchronously execute a query.</span></span> <span data-ttu-id="4e3b6-157">這些方法的範例包括 ToListAsync、FirstAsync、ForEachAsync 等等。</span><span class="sxs-lookup"><span data-stu-id="4e3b6-157">Examples of these methods include ToListAsync, FirstAsync, ForEachAsync, etc.</span></span>  

<span data-ttu-id="4e3b6-158">因為 Entity Framework 的查詢會使用 LINQ，所以會在 IQueryable 和 IEnumerable 上定義擴充方法。</span><span class="sxs-lookup"><span data-stu-id="4e3b6-158">Because Entity Framework queries make use of LINQ, the extension methods are defined on IQueryable and IEnumerable.</span></span> <span data-ttu-id="4e3b6-159">不過，因為它們只設計用來搭配 Entity Framework 如果您嘗試在不是 Entity Framework 查詢的 LINQ 查詢上使用它們，您可能會收到下列錯誤：</span><span class="sxs-lookup"><span data-stu-id="4e3b6-159">However, because they are only designed to be used with Entity Framework you may receive the following error if you try to use them on a LINQ query that isn’t an Entity Framework query:</span></span>

> <span data-ttu-id="4e3b6-160">來源 IQueryable 不會執行 IDbAsyncEnumerable {0} 。</span><span class="sxs-lookup"><span data-stu-id="4e3b6-160">The source IQueryable doesn't implement IDbAsyncEnumerable{0}.</span></span> <span data-ttu-id="4e3b6-161">只有執行 IDbAsyncEnumerable 的來源可以用於 Entity Framework 非同步作業。</span><span class="sxs-lookup"><span data-stu-id="4e3b6-161">Only sources that implement IDbAsyncEnumerable can be used for Entity Framework asynchronous operations.</span></span> <span data-ttu-id="4e3b6-162">如需詳細資訊，請參閱 [http://go.microsoft.com/fwlink/?LinkId=287068](https://go.microsoft.com/fwlink/?LinkId=287068) 。</span><span class="sxs-lookup"><span data-stu-id="4e3b6-162">For more details see [http://go.microsoft.com/fwlink/?LinkId=287068](https://go.microsoft.com/fwlink/?LinkId=287068).</span></span>  

<span data-ttu-id="4e3b6-163">雖然非同步方法只有在針對 EF 查詢執行時才受支援，但您可能會想要在針對 DbSet 的記憶體中測試雙精度執行時，在單元測試中使用這些方法。</span><span class="sxs-lookup"><span data-stu-id="4e3b6-163">Whilst the async methods are only supported when running against an EF query, you may want to use them in your unit test when running against an in-memory test double of a DbSet.</span></span>  

<span data-ttu-id="4e3b6-164">若要使用非同步方法，我們必須建立記憶體中的 DbAsyncQueryProvider 來處理非同步查詢。</span><span class="sxs-lookup"><span data-stu-id="4e3b6-164">In order to use the async methods we need to create an in-memory DbAsyncQueryProvider to process the async query.</span></span> <span data-ttu-id="4e3b6-165">雖然您可以使用 Moq 來設定查詢提供者，但在程式碼中建立測試雙精度更容易。</span><span class="sxs-lookup"><span data-stu-id="4e3b6-165">Whilst it would be possible to setup a query provider using Moq, it is much easier to create a test double implementation in code.</span></span> <span data-ttu-id="4e3b6-166">這項實行的程式碼如下所示：</span><span class="sxs-lookup"><span data-stu-id="4e3b6-166">The code for this implementation is as follows:</span></span>  

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

<span data-ttu-id="4e3b6-167">現在我們已經有了非同步查詢提供者，我們可以為新的 GetAllBlogsAsync 方法撰寫單元測試。</span><span class="sxs-lookup"><span data-stu-id="4e3b6-167">Now that we have an async query provider we can write a unit test for our new GetAllBlogsAsync method.</span></span>  

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
