---
title: 交易的 EF 核心
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: d3e6515b-8181-482c-a790-c4a6778748c1
ms.technology: entity-framework-core
uid: core/saving/transactions
ms.openlocfilehash: fe4c0d6ad7ccb2e97dc94fbf2eb26a41e7fbcb19
ms.sourcegitcommit: 7113e8675f26cbb546200824512078bf360225df
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2018
---
# <a name="using-transactions"></a><span data-ttu-id="9c628-102">使用交易</span><span class="sxs-lookup"><span data-stu-id="9c628-102">Using Transactions</span></span>

<span data-ttu-id="9c628-103">交易可讓數個資料庫作業，以不可部分完成的方式處理。</span><span class="sxs-lookup"><span data-stu-id="9c628-103">Transactions allow several database operations to be processed in an atomic manner.</span></span> <span data-ttu-id="9c628-104">認可交易時，如果所有的作業會成功套用至資料庫中。</span><span class="sxs-lookup"><span data-stu-id="9c628-104">If the transaction is committed, all of the operations are successfully applied to the database.</span></span> <span data-ttu-id="9c628-105">如果回復交易，所有作業都不會套用至資料庫。</span><span class="sxs-lookup"><span data-stu-id="9c628-105">If the transaction is rolled back, none of the operations are applied to the database.</span></span>

> [!TIP]  
> <span data-ttu-id="9c628-106">您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/Transactions/) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="9c628-106">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/Transactions/) on GitHub.</span></span>

## <a name="default-transaction-behavior"></a><span data-ttu-id="9c628-107">預設的交易行為</span><span class="sxs-lookup"><span data-stu-id="9c628-107">Default transaction behavior</span></span>

<span data-ttu-id="9c628-108">根據預設，如果資料庫提供者支援交易，所有變更的單一呼叫中`SaveChanges()`會套用在交易中。</span><span class="sxs-lookup"><span data-stu-id="9c628-108">By default, if the database provider supports transactions, all changes in a single call to `SaveChanges()` are applied in a transaction.</span></span> <span data-ttu-id="9c628-109">如果有任何變更失敗，然後回復交易，並變更不會套用至資料庫。</span><span class="sxs-lookup"><span data-stu-id="9c628-109">If any of the changes fail, then the transaction is rolled back and none of the changes are applied to the database.</span></span> <span data-ttu-id="9c628-110">這表示`SaveChanges()`保證完全成功，或讓資料庫發生錯誤時，不想修改。</span><span class="sxs-lookup"><span data-stu-id="9c628-110">This means that `SaveChanges()` is guaranteed to either completely succeed, or leave the database unmodified if an error occurs.</span></span>

<span data-ttu-id="9c628-111">對於大部分的應用程式，此預設行為已足夠。</span><span class="sxs-lookup"><span data-stu-id="9c628-111">For most applications, this default behavior is sufficient.</span></span> <span data-ttu-id="9c628-112">如果應用程式的需求覺得有必要，您應該僅以手動方式控制交易。</span><span class="sxs-lookup"><span data-stu-id="9c628-112">You should only manually control transactions if your application requirements deem it necessary.</span></span>

## <a name="controlling-transactions"></a><span data-ttu-id="9c628-113">控制交易</span><span class="sxs-lookup"><span data-stu-id="9c628-113">Controlling transactions</span></span>

<span data-ttu-id="9c628-114">您可以使用`DbContext.Database`API 來開始、 認可和回復交易。</span><span class="sxs-lookup"><span data-stu-id="9c628-114">You can use the `DbContext.Database` API to begin, commit, and rollback transactions.</span></span> <span data-ttu-id="9c628-115">下列範例示範兩個`SaveChanges()`作業和 LINQ 查詢在單一交易中執行。</span><span class="sxs-lookup"><span data-stu-id="9c628-115">The following example shows two `SaveChanges()` operations and a LINQ query being executed in a single transaction.</span></span>

<span data-ttu-id="9c628-116">並非所有的資料庫提供者支援交易。</span><span class="sxs-lookup"><span data-stu-id="9c628-116">Not all database providers support transactions.</span></span> <span data-ttu-id="9c628-117">某些提供者可能會擲回或無作業 Api 呼叫交易時。</span><span class="sxs-lookup"><span data-stu-id="9c628-117">Some providers may throw or no-op when transaction APIs are called.</span></span>

<!-- [!code-csharp[Main](samples/core/Saving/Saving/Transactions/ControllingTransaction/Sample.cs?highlight=3,17,18,19)] -->
``` csharp
        using (var context = new BloggingContext())
        {
            using (var transaction = context.Database.BeginTransaction())
            {
                try
                {
                    context.Blogs.Add(new Blog { Url = "http://blogs.msdn.com/dotnet" });
                    context.SaveChanges();

                    context.Blogs.Add(new Blog { Url = "http://blogs.msdn.com/visualstudio" });
                    context.SaveChanges();

                    var blogs = context.Blogs
                        .OrderBy(b => b.Url)
                        .ToList();

                    // Commit transaction if all commands succeed, transaction will auto-rollback
                    // when disposed if either commands fails
                    transaction.Commit();
                }
                catch (Exception)
                {
                    // TODO: Handle failure
                }
            }
        }
```

## <a name="cross-context-transaction-relational-databases-only"></a><span data-ttu-id="9c628-118">跨內容交易 （只有關聯式資料庫）</span><span class="sxs-lookup"><span data-stu-id="9c628-118">Cross-context transaction (relational databases only)</span></span>

<span data-ttu-id="9c628-119">您也可以在多個內容執行個體之間共用的交易。</span><span class="sxs-lookup"><span data-stu-id="9c628-119">You can also share a transaction across multiple context instances.</span></span> <span data-ttu-id="9c628-120">這項功能時才使用關聯式資料庫提供者，因為它需要使用`DbTransaction`和`DbConnection`，這專屬於關聯式資料庫。</span><span class="sxs-lookup"><span data-stu-id="9c628-120">This functionality is only available when using a relational database provider because it requires the use of `DbTransaction` and `DbConnection`, which are specific to relational databases.</span></span>

<span data-ttu-id="9c628-121">若要共用交易，內容必須共用兩者`DbConnection`和`DbTransaction`。</span><span class="sxs-lookup"><span data-stu-id="9c628-121">To share a transaction, the contexts must share both a `DbConnection` and a `DbTransaction`.</span></span>

### <a name="allow-connection-to-be-externally-provided"></a><span data-ttu-id="9c628-122">允許從外部提供的連線</span><span class="sxs-lookup"><span data-stu-id="9c628-122">Allow connection to be externally provided</span></span>

<span data-ttu-id="9c628-123">共用`DbConnection`需要建構它時，將連接傳遞到內容的功能。</span><span class="sxs-lookup"><span data-stu-id="9c628-123">Sharing a `DbConnection` requires the ability to pass a connection into a context when constructing it.</span></span>

<span data-ttu-id="9c628-124">允許的最簡單方式`DbConnection`外部提供，是要停止使用`DbContext.OnConfiguring`方法來設定內容和外部建立`DbContextOptions`並將其傳遞至內容的建構函式。</span><span class="sxs-lookup"><span data-stu-id="9c628-124">The easiest way to allow `DbConnection` to be externally provided, is to stop using the `DbContext.OnConfiguring` method to configure the context and externally create `DbContextOptions` and pass them to the context constructor.</span></span>

> [!TIP]  
> <span data-ttu-id="9c628-125">`DbContextOptionsBuilder` 是您在中使用的 API`DbContext.OnConfiguring`若要設定內容，您現在要建立外部使用`DbContextOptions`。</span><span class="sxs-lookup"><span data-stu-id="9c628-125">`DbContextOptionsBuilder` is the API you used in `DbContext.OnConfiguring` to configure the context, you are now going to use it externally to create `DbContextOptions`.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Transactions/SharingTransaction/Sample.cs?name=Context&highlight=3,4,5)]

<span data-ttu-id="9c628-126">若要繼續使用替代方法是`DbContext.OnConfiguring`，但接受`DbConnection`，是儲存並接著用於`DbContext.OnConfiguring`。</span><span class="sxs-lookup"><span data-stu-id="9c628-126">An alternative is to keep using `DbContext.OnConfiguring`, but accept a `DbConnection` that is saved and then used in `DbContext.OnConfiguring`.</span></span>

``` csharp
public class BloggingContext : DbContext
{
    private DbConnection _connection;

    public BloggingContext(DbConnection connection)
    {
      _connection = connection;
    }

    public DbSet<Blog> Blogs { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseSqlServer(_connection);
    }
}
```

### <a name="share-connection-and-transaction"></a><span data-ttu-id="9c628-127">共用連接及交易</span><span class="sxs-lookup"><span data-stu-id="9c628-127">Share connection and transaction</span></span>

<span data-ttu-id="9c628-128">您現在可以建立多個共用相同連接的內容執行個體。</span><span class="sxs-lookup"><span data-stu-id="9c628-128">You can now create multiple context instances that share the same connection.</span></span> <span data-ttu-id="9c628-129">然後使用`DbContext.Database.UseTransaction(DbTransaction)`API 來編列這兩個相同交易中的內容。</span><span class="sxs-lookup"><span data-stu-id="9c628-129">Then use the `DbContext.Database.UseTransaction(DbTransaction)` API to enlist both contexts in the same transaction.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Transactions/SharingTransaction/Sample.cs?name=Transaction&highlight=1,2,3,7,16,23,24,25)]

## <a name="using-external-dbtransactions-relational-databases-only"></a><span data-ttu-id="9c628-130">使用外部 DbTransactions （只有關聯式資料庫）</span><span class="sxs-lookup"><span data-stu-id="9c628-130">Using external DbTransactions (relational databases only)</span></span>

<span data-ttu-id="9c628-131">如果您使用多個資料存取技術來存取關聯式資料庫，您可能想要共用這些不同的技術所執行作業之間的交易。</span><span class="sxs-lookup"><span data-stu-id="9c628-131">If you are using multiple data access technologies to access a relational database, you may want to share a transaction between operations performed by these different technologies.</span></span>

<span data-ttu-id="9c628-132">下列範例中，示範如何在相同交易中執行 ADO.NET SqlClient 和 Entity Framework Core 作業。</span><span class="sxs-lookup"><span data-stu-id="9c628-132">The following example, shows how to perform an ADO.NET SqlClient operation and an Entity Framework Core operation in the same transaction.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Transactions/ExternalDbTransaction/Sample.cs?name=Transaction&highlight=4,10,21,26,27,28)]

## <a name="using-systemtransactions"></a><span data-ttu-id="9c628-133">使用 System.Transactions</span><span class="sxs-lookup"><span data-stu-id="9c628-133">Using System.Transactions</span></span>

> [!NOTE]  
> <span data-ttu-id="9c628-134">這項功能是在 EF 核心 2.1 中新功能。</span><span class="sxs-lookup"><span data-stu-id="9c628-134">This feature is new in EF Core 2.1.</span></span>

<span data-ttu-id="9c628-135">您可使用環境交易，如果您需要在較大範圍協調。</span><span class="sxs-lookup"><span data-stu-id="9c628-135">It is possible to use ambient transactions if you need to coordinate across a larger scope.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Transactions/AmbientTransaction/Sample.cs?name=Transaction&highlight=1,24,25,26)]

<span data-ttu-id="9c628-136">它也可在明確交易中登記。</span><span class="sxs-lookup"><span data-stu-id="9c628-136">It is also possible to enlist in an explicit transaction.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Transactions/CommitableTransaction/Sample.cs?name=Transaction&highlight=1,13,26,27,28)]

### <a name="limitations-of-systemtransactions"></a><span data-ttu-id="9c628-137">System.Transactions 的限制</span><span class="sxs-lookup"><span data-stu-id="9c628-137">Limitations of System.Transactions</span></span>  

1. <span data-ttu-id="9c628-138">EF 核心依賴資料庫提供者實作 System.Transactions 的支援。</span><span class="sxs-lookup"><span data-stu-id="9c628-138">EF Core relies on database providers to implement support for System.Transactions.</span></span> <span data-ttu-id="9c628-139">雖然支援是很常見的 ADO.NET 提供者的.NET Framework API 只已最近新增到.NET Core，因此支援是無法為廣泛。</span><span class="sxs-lookup"><span data-stu-id="9c628-139">Although support is quite common among ADO.NET providers for .NET Framework, the API has only been recently added to .NET Core and hence support is not be as widespread.</span></span> <span data-ttu-id="9c628-140">如果提供者未實作 system.transactions 的支援，則可能會完全忽略這些 Api 的呼叫。</span><span class="sxs-lookup"><span data-stu-id="9c628-140">If a provider does not implement support for System.Transactions, it is possible that calls to these APIs will be completely ignored.</span></span> <span data-ttu-id="9c628-141">適用於.NET Core 的 SqlClient 支援它從 2.1 及更新版本。</span><span class="sxs-lookup"><span data-stu-id="9c628-141">SqlClient for .NET Core does support it from 2.1 onwards.</span></span> <span data-ttu-id="9c628-142">適用於.NET Core 2.0 SqlClient 將會擲回例外狀況的您嘗試使用此功能。</span><span class="sxs-lookup"><span data-stu-id="9c628-142">SqlClient for .NET Core 2.0 will throw an exception of you attempt to use the feature.</span></span> 

   > [!IMPORTANT]  
   > <span data-ttu-id="9c628-143">我們建議您測試的資料，應用程式開發介面的行為會正確地與您的提供者之前您仰賴它來管理交易。</span><span class="sxs-lookup"><span data-stu-id="9c628-143">It is recommended that you test that the API behaves correctly with your provider before you rely on it for managing transactions.</span></span> <span data-ttu-id="9c628-144">您是我們建議如果不存在，請連絡資料庫提供者維護程式。</span><span class="sxs-lookup"><span data-stu-id="9c628-144">You are encouraged to contact the maintainer of the database provider if it does not.</span></span> 

2. <span data-ttu-id="9c628-145">從 2.1 版開始，.NET Core 的 System.Transactions 實作不包含分散式交易支援，因此您無法使用`TransactionScope`或`CommitableTransaction`跨多個資源管理員協調的交易。</span><span class="sxs-lookup"><span data-stu-id="9c628-145">As of version 2.1, the System.Transactions implementation in .NET Core does not include support for distributed transactions, therefore you cannot use `TransactionScope` or `CommitableTransaction` to coordinate transactions across multiple resource managers.</span></span> 
