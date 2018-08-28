---
title: 交易 - EF Core
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: d3e6515b-8181-482c-a790-c4a6778748c1
ms.technology: entity-framework-core
uid: core/saving/transactions
ms.openlocfilehash: 6e6ded74e15187b387e8e0b2ad00cb47a84ff7e8
ms.sourcegitcommit: 6cf6493d81b6d81b0b0f37a00e0fc23ec7189158
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/12/2018
ms.locfileid: "42447653"
---
# <a name="using-transactions"></a><span data-ttu-id="9afe4-102">使用交易</span><span class="sxs-lookup"><span data-stu-id="9afe4-102">Using Transactions</span></span>

<span data-ttu-id="9afe4-103">交易可讓系統以不可部分完成的方式處理數個資料庫作業。</span><span class="sxs-lookup"><span data-stu-id="9afe4-103">Transactions allow several database operations to be processed in an atomic manner.</span></span> <span data-ttu-id="9afe4-104">如果認可交易，就會對資料庫成功套用所有作業。</span><span class="sxs-lookup"><span data-stu-id="9afe4-104">If the transaction is committed, all of the operations are successfully applied to the database.</span></span> <span data-ttu-id="9afe4-105">如果復原交易，則不會對資料庫套用任何作業。</span><span class="sxs-lookup"><span data-stu-id="9afe4-105">If the transaction is rolled back, none of the operations are applied to the database.</span></span>

> [!TIP]  
> <span data-ttu-id="9afe4-106">您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/Transactions/) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="9afe4-106">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/Transactions/) on GitHub.</span></span>

## <a name="default-transaction-behavior"></a><span data-ttu-id="9afe4-107">預設交易行為</span><span class="sxs-lookup"><span data-stu-id="9afe4-107">Default transaction behavior</span></span>

<span data-ttu-id="9afe4-108">根據預設，如果資料庫提供者支援交易，就會在交易中套用對 `SaveChanges()` 單一呼叫中的所有變更。</span><span class="sxs-lookup"><span data-stu-id="9afe4-108">By default, if the database provider supports transactions, all changes in a single call to `SaveChanges()` are applied in a transaction.</span></span> <span data-ttu-id="9afe4-109">如果其中任何一項變更失敗，系統就會復原交易，而不會對資料庫套用任何變更。</span><span class="sxs-lookup"><span data-stu-id="9afe4-109">If any of the changes fail, then the transaction is rolled back and none of the changes are applied to the database.</span></span> <span data-ttu-id="9afe4-110">這意謂著會保證 `SaveChanges()` 要不就是完全成功，要不就是發生錯誤時讓資料庫維持原封不動。</span><span class="sxs-lookup"><span data-stu-id="9afe4-110">This means that `SaveChanges()` is guaranteed to either completely succeed, or leave the database unmodified if an error occurs.</span></span>

<span data-ttu-id="9afe4-111">對大多數應用程式來說，此預設行為已足以應付需求。</span><span class="sxs-lookup"><span data-stu-id="9afe4-111">For most applications, this default behavior is sufficient.</span></span> <span data-ttu-id="9afe4-112">您應該只有在應用程式需求認為有必要時，才手動控制交易。</span><span class="sxs-lookup"><span data-stu-id="9afe4-112">You should only manually control transactions if your application requirements deem it necessary.</span></span>

## <a name="controlling-transactions"></a><span data-ttu-id="9afe4-113">控制交易</span><span class="sxs-lookup"><span data-stu-id="9afe4-113">Controlling transactions</span></span>

<span data-ttu-id="9afe4-114">您可以使用 `DbContext.Database` API 來開始、認可及復原交易。</span><span class="sxs-lookup"><span data-stu-id="9afe4-114">You can use the `DbContext.Database` API to begin, commit, and rollback transactions.</span></span> <span data-ttu-id="9afe4-115">下列範例示範在單一交易中執行兩個 `SaveChanges()` 作業和一個 LINQ 查詢。</span><span class="sxs-lookup"><span data-stu-id="9afe4-115">The following example shows two `SaveChanges()` operations and a LINQ query being executed in a single transaction.</span></span>

<span data-ttu-id="9afe4-116">並非所有資料庫提供者都支援交易。</span><span class="sxs-lookup"><span data-stu-id="9afe4-116">Not all database providers support transactions.</span></span> <span data-ttu-id="9afe4-117">呼叫交易 API 時，有些提供者可能會擲回例外狀況或不執行任何作業。</span><span class="sxs-lookup"><span data-stu-id="9afe4-117">Some providers may throw or no-op when transaction APIs are called.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Transactions/ControllingTransaction/Sample.cs?name=Transaction&highlight=3,17,18,19)]

## <a name="cross-context-transaction-relational-databases-only"></a><span data-ttu-id="9afe4-118">跨內容交易 (僅適用於關聯式資料庫)</span><span class="sxs-lookup"><span data-stu-id="9afe4-118">Cross-context transaction (relational databases only)</span></span>

<span data-ttu-id="9afe4-119">您也可以跨多個內容執行個體共用交易。</span><span class="sxs-lookup"><span data-stu-id="9afe4-119">You can also share a transaction across multiple context instances.</span></span> <span data-ttu-id="9afe4-120">只有使用關聯式資料庫提供者時才有提供此功能，因為它會要求使用關聯式資料庫資料庫特定的 `DbTransaction` 和 `DbConnection`。</span><span class="sxs-lookup"><span data-stu-id="9afe4-120">This functionality is only available when using a relational database provider because it requires the use of `DbTransaction` and `DbConnection`, which are specific to relational databases.</span></span>

<span data-ttu-id="9afe4-121">若要共用交易，內容必須同時共用 `DbConnection` 和 `DbTransaction`。</span><span class="sxs-lookup"><span data-stu-id="9afe4-121">To share a transaction, the contexts must share both a `DbConnection` and a `DbTransaction`.</span></span>

### <a name="allow-connection-to-be-externally-provided"></a><span data-ttu-id="9afe4-122">允許從外部提供連線</span><span class="sxs-lookup"><span data-stu-id="9afe4-122">Allow connection to be externally provided</span></span>

<span data-ttu-id="9afe4-123">必須能夠在建構內容時將連線傳遞給內容，才能共用 `DbConnection`。</span><span class="sxs-lookup"><span data-stu-id="9afe4-123">Sharing a `DbConnection` requires the ability to pass a connection into a context when constructing it.</span></span>

<span data-ttu-id="9afe4-124">若要允許從外部提供 `DbConnection`，最簡單的方式就是停止使用 `DbContext.OnConfiguring` 方法來設定內容，然後從外部建立 `DbContextOptions` 並將其傳遞給內容建構函式。</span><span class="sxs-lookup"><span data-stu-id="9afe4-124">The easiest way to allow `DbConnection` to be externally provided, is to stop using the `DbContext.OnConfiguring` method to configure the context and externally create `DbContextOptions` and pass them to the context constructor.</span></span>

> [!TIP]  
> <span data-ttu-id="9afe4-125">`DbContextOptionsBuilder` 是您在 `DbContext.OnConfiguring` 中用來設定內容的 API，現在您將從外部使用它來建立 `DbContextOptions`。</span><span class="sxs-lookup"><span data-stu-id="9afe4-125">`DbContextOptionsBuilder` is the API you used in `DbContext.OnConfiguring` to configure the context, you are now going to use it externally to create `DbContextOptions`.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Transactions/SharingTransaction/Sample.cs?name=Context&highlight=3,4,5)]

<span data-ttu-id="9afe4-126">替代方法是繼續使用 `DbContext.OnConfiguring`，但接受 `DbConnection`，這會在儲存後於 `DbContext.OnConfiguring` 中使用。</span><span class="sxs-lookup"><span data-stu-id="9afe4-126">An alternative is to keep using `DbContext.OnConfiguring`, but accept a `DbConnection` that is saved and then used in `DbContext.OnConfiguring`.</span></span>

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

### <a name="share-connection-and-transaction"></a><span data-ttu-id="9afe4-127">共用連線和交易</span><span class="sxs-lookup"><span data-stu-id="9afe4-127">Share connection and transaction</span></span>

<span data-ttu-id="9afe4-128">您現在可以建立多個共用相同連線的內容執行個體。</span><span class="sxs-lookup"><span data-stu-id="9afe4-128">You can now create multiple context instances that share the same connection.</span></span> <span data-ttu-id="9afe4-129">然後使用 `DbContext.Database.UseTransaction(DbTransaction)` API 將兩個內容都登錄在同一個交易中。</span><span class="sxs-lookup"><span data-stu-id="9afe4-129">Then use the `DbContext.Database.UseTransaction(DbTransaction)` API to enlist both contexts in the same transaction.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Transactions/SharingTransaction/Sample.cs?name=Transaction&highlight=1,2,3,7,16,23,24,25)]

## <a name="using-external-dbtransactions-relational-databases-only"></a><span data-ttu-id="9afe4-130">使用外部 DbTransactions (僅適用於關聯式資料庫)</span><span class="sxs-lookup"><span data-stu-id="9afe4-130">Using external DbTransactions (relational databases only)</span></span>

<span data-ttu-id="9afe4-131">如果您使用多個資料存取技術來存取關聯式資料庫，則可能會想要在這些不同技術所執行的作業之間共用交易。</span><span class="sxs-lookup"><span data-stu-id="9afe4-131">If you are using multiple data access technologies to access a relational database, you may want to share a transaction between operations performed by these different technologies.</span></span>

<span data-ttu-id="9afe4-132">下列範例示範如何在同一個交易中執行 ADO.NET SqlClient 作業和 Entity Framework Core 作業。</span><span class="sxs-lookup"><span data-stu-id="9afe4-132">The following example, shows how to perform an ADO.NET SqlClient operation and an Entity Framework Core operation in the same transaction.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Transactions/ExternalDbTransaction/Sample.cs?name=Transaction&highlight=4,10,21,26,27,28)]

## <a name="using-systemtransactions"></a><span data-ttu-id="9afe4-133">使用 System.Transactions</span><span class="sxs-lookup"><span data-stu-id="9afe4-133">Using System.Transactions</span></span>

> [!NOTE]  
> <span data-ttu-id="9afe4-134">此功能是 EF Core 2.1 中的新功能。</span><span class="sxs-lookup"><span data-stu-id="9afe4-134">This feature is new in EF Core 2.1.</span></span>

<span data-ttu-id="9afe4-135">如果您需要跨較大的範圍進行協調，可以使用環境交易。</span><span class="sxs-lookup"><span data-stu-id="9afe4-135">It is possible to use ambient transactions if you need to coordinate across a larger scope.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Transactions/AmbientTransaction/Sample.cs?name=Transaction&highlight=1,2,3,26,27,28)]

<span data-ttu-id="9afe4-136">此外，也可以登錄在明確交易中。</span><span class="sxs-lookup"><span data-stu-id="9afe4-136">It is also possible to enlist in an explicit transaction.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Transactions/CommitableTransaction/Sample.cs?name=Transaction&highlight=1,15,28,29,30)]

### <a name="limitations-of-systemtransactions"></a><span data-ttu-id="9afe4-137">System.Transactions 的限制</span><span class="sxs-lookup"><span data-stu-id="9afe4-137">Limitations of System.Transactions</span></span>  

1. <span data-ttu-id="9afe4-138">EF Core 需倚賴資料庫提供者實作對 System.Transactions 的支援。</span><span class="sxs-lookup"><span data-stu-id="9afe4-138">EF Core relies on database providers to implement support for System.Transactions.</span></span> <span data-ttu-id="9afe4-139">雖然支援在 .NET Framework 的 ADO.NET 提供者之間很常見，但此 API 是最近才新增至 .NET Core 中，因此支援尚不普遍。</span><span class="sxs-lookup"><span data-stu-id="9afe4-139">Although support is quite common among ADO.NET providers for .NET Framework, the API has only been recently added to .NET Core and hence support is not as widespread.</span></span> <span data-ttu-id="9afe4-140">如果提供者未實作對 System.Transactions 的支援，則對這些 API 發出的呼叫將可能完全被忽略。</span><span class="sxs-lookup"><span data-stu-id="9afe4-140">If a provider does not implement support for System.Transactions, it is possible that calls to these APIs will be completely ignored.</span></span> <span data-ttu-id="9afe4-141">.NET Core 的 SqlClient 從 2.1 版起便不支援它。</span><span class="sxs-lookup"><span data-stu-id="9afe4-141">SqlClient for .NET Core does support it from 2.1 onwards.</span></span> <span data-ttu-id="9afe4-142">.NET Core 2.0 的 SqlClient 會在您嘗試使用該功能時擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="9afe4-142">SqlClient for .NET Core 2.0 will throw an exception of you attempt to use the feature.</span></span> 

   > [!IMPORTANT]  
   > <span data-ttu-id="9afe4-143">建議您先測試該 API 是否可與您的提供者正確搭配運作，再倚賴它來管理交易。</span><span class="sxs-lookup"><span data-stu-id="9afe4-143">It is recommended that you test that the API behaves correctly with your provider before you rely on it for managing transactions.</span></span> <span data-ttu-id="9afe4-144">如果無法正確搭配運作，建議您與資料庫提供者的維護人員連絡。</span><span class="sxs-lookup"><span data-stu-id="9afe4-144">You are encouraged to contact the maintainer of the database provider if it does not.</span></span> 

2. <span data-ttu-id="9afe4-145">從 2.1 版開始，.NET Core 中的 System.Transactions 實作便不包含對分散式交易的支援，因此您無法使用 `TransactionScope` 或 `CommitableTransaction` 來跨多個資源管理員協調交易。</span><span class="sxs-lookup"><span data-stu-id="9afe4-145">As of version 2.1, the System.Transactions implementation in .NET Core does not include support for distributed transactions, therefore you cannot use `TransactionScope` or `CommitableTransaction` to coordinate transactions across multiple resource managers.</span></span> 
