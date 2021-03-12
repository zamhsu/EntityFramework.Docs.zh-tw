---
title: 交易 - EF Core
description: 使用 Entity Framework Core 儲存資料時，管理不可部分完成的交易
author: roji
ms.date: 9/26/2020
uid: core/saving/transactions
ms.openlocfilehash: 25bf615cffa36384de9fe04fe2b84d4a6bca98d0
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/11/2021
ms.locfileid: "103023532"
---
# <a name="using-transactions"></a><span data-ttu-id="946b2-103">使用交易</span><span class="sxs-lookup"><span data-stu-id="946b2-103">Using Transactions</span></span>

<span data-ttu-id="946b2-104">交易可讓系統以不可部分完成的方式處理數個資料庫作業。</span><span class="sxs-lookup"><span data-stu-id="946b2-104">Transactions allow several database operations to be processed in an atomic manner.</span></span> <span data-ttu-id="946b2-105">如果認可交易，就會對資料庫成功套用所有作業。</span><span class="sxs-lookup"><span data-stu-id="946b2-105">If the transaction is committed, all of the operations are successfully applied to the database.</span></span> <span data-ttu-id="946b2-106">如果復原交易，則不會對資料庫套用任何作業。</span><span class="sxs-lookup"><span data-stu-id="946b2-106">If the transaction is rolled back, none of the operations are applied to the database.</span></span>

> [!TIP]
> <span data-ttu-id="946b2-107">您可以在 GitHub 上檢視此文章的[範例](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Saving/Transactions/) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="946b2-107">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Saving/Transactions/) on GitHub.</span></span>

## <a name="default-transaction-behavior"></a><span data-ttu-id="946b2-108">預設交易行為</span><span class="sxs-lookup"><span data-stu-id="946b2-108">Default transaction behavior</span></span>

<span data-ttu-id="946b2-109">根據預設，如果資料庫提供者支援交易，就會在交易中套用對 `SaveChanges` 單一呼叫中的所有變更。</span><span class="sxs-lookup"><span data-stu-id="946b2-109">By default, if the database provider supports transactions, all changes in a single call to `SaveChanges` are applied in a transaction.</span></span> <span data-ttu-id="946b2-110">如果其中任何一項變更失敗，系統就會復原交易，而不會對資料庫套用任何變更。</span><span class="sxs-lookup"><span data-stu-id="946b2-110">If any of the changes fail, then the transaction is rolled back and none of the changes are applied to the database.</span></span> <span data-ttu-id="946b2-111">這意謂著會保證 `SaveChanges` 要不就是完全成功，要不就是發生錯誤時讓資料庫維持原封不動。</span><span class="sxs-lookup"><span data-stu-id="946b2-111">This means that `SaveChanges` is guaranteed to either completely succeed, or leave the database unmodified if an error occurs.</span></span>

<span data-ttu-id="946b2-112">對大多數應用程式來說，此預設行為已足以應付需求。</span><span class="sxs-lookup"><span data-stu-id="946b2-112">For most applications, this default behavior is sufficient.</span></span> <span data-ttu-id="946b2-113">您應該只有在應用程式需求認為有必要時，才手動控制交易。</span><span class="sxs-lookup"><span data-stu-id="946b2-113">You should only manually control transactions if your application requirements deem it necessary.</span></span>

## <a name="controlling-transactions"></a><span data-ttu-id="946b2-114">控制交易</span><span class="sxs-lookup"><span data-stu-id="946b2-114">Controlling transactions</span></span>

<span data-ttu-id="946b2-115">您可以使用 `DbContext.Database` API 來開始、認可及復原交易。</span><span class="sxs-lookup"><span data-stu-id="946b2-115">You can use the `DbContext.Database` API to begin, commit, and rollback transactions.</span></span> <span data-ttu-id="946b2-116">下列範例顯示 `SaveChanges` 在單一交易中執行的兩個作業和一個 LINQ 查詢：</span><span class="sxs-lookup"><span data-stu-id="946b2-116">The following example shows two `SaveChanges` operations and a LINQ query being executed in a single transaction:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/ControllingTransaction.cs?name=Transaction&highlight=2,16-18)]

<span data-ttu-id="946b2-117">當所有關系資料庫提供者都支援交易時，當呼叫交易 Api 時，其他提供者類型可能會擲回或無作業。</span><span class="sxs-lookup"><span data-stu-id="946b2-117">While all relational database providers support transactions, other providers types may throw or no-op when transaction APIs are called.</span></span>

## <a name="savepoints"></a><span data-ttu-id="946b2-118">點</span><span class="sxs-lookup"><span data-stu-id="946b2-118">Savepoints</span></span>

> [!NOTE]
> <span data-ttu-id="946b2-119">這項功能是在 EF Core 5.0 中引進。</span><span class="sxs-lookup"><span data-stu-id="946b2-119">This feature was introduced in EF Core 5.0.</span></span>

<span data-ttu-id="946b2-120">當叫用， `SaveChanges` 且內容中已有交易正在進行時，EF 會自動建立儲存 *點* ，然後再儲存任何資料。</span><span class="sxs-lookup"><span data-stu-id="946b2-120">When `SaveChanges` is invoked and a transaction is already in progress on the context, EF automatically creates a *savepoint* before saving any data.</span></span> <span data-ttu-id="946b2-121">儲存點是資料庫交易內的點，如果發生錯誤或因任何其他原因，可能會在稍後回復至。</span><span class="sxs-lookup"><span data-stu-id="946b2-121">Savepoints are points within a database transaction which may later be rolled back to, if an error occurs or for any other reason.</span></span> <span data-ttu-id="946b2-122">如果 `SaveChanges` 遇到任何錯誤，它會自動將交易回復回儲存點，讓交易保持與從未啟動的相同狀態。</span><span class="sxs-lookup"><span data-stu-id="946b2-122">If `SaveChanges` encounters any error, it automatically rolls the transaction back to the savepoint, leaving the transaction in the same state as if it had never started.</span></span> <span data-ttu-id="946b2-123">這可讓您在發生 [開放式並行](xref:core/saving/concurrency) 存取問題時，可能會更正問題並重試儲存。</span><span class="sxs-lookup"><span data-stu-id="946b2-123">This allows you to possibly correct issues and retry saving, in particular when [optimistic concurrency](xref:core/saving/concurrency) issues occur.</span></span>

> [!WARNING]
> <span data-ttu-id="946b2-124">儲存點與 SQL Server 的 Multiple Active Result Sets 不相容，且不會使用。</span><span class="sxs-lookup"><span data-stu-id="946b2-124">Savepoints are incompatible with SQL Server's Multiple Active Result Sets, and are not used.</span></span> <span data-ttu-id="946b2-125">如果發生錯誤 `SaveChanges` ，交易可能會處於未知的狀態。</span><span class="sxs-lookup"><span data-stu-id="946b2-125">If an error occurs during `SaveChanges`, the transaction may be left in an unknown state.</span></span>

<span data-ttu-id="946b2-126">您也可以手動管理儲存點，就像使用交易一樣。</span><span class="sxs-lookup"><span data-stu-id="946b2-126">It's also possible to manually manage savepoints, just as it is with transactions.</span></span> <span data-ttu-id="946b2-127">下列範例會在交易中建立儲存點，並在失敗時回復至該儲存點：</span><span class="sxs-lookup"><span data-stu-id="946b2-127">The following example creates a savepoint within a transaction, and rolls back to it on failure:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/ManagingSavepoints.cs?name=Savepoints&highlight=9,19-20)]

## <a name="cross-context-transaction"></a><span data-ttu-id="946b2-128">跨內容交易</span><span class="sxs-lookup"><span data-stu-id="946b2-128">Cross-context transaction</span></span>

<span data-ttu-id="946b2-129">您也可以跨多個內容執行個體共用交易。</span><span class="sxs-lookup"><span data-stu-id="946b2-129">You can also share a transaction across multiple context instances.</span></span> <span data-ttu-id="946b2-130">只有使用關聯式資料庫提供者時才有提供此功能，因為它會要求使用關聯式資料庫資料庫特定的 `DbTransaction` 和 `DbConnection`。</span><span class="sxs-lookup"><span data-stu-id="946b2-130">This functionality is only available when using a relational database provider because it requires the use of `DbTransaction` and `DbConnection`, which are specific to relational databases.</span></span>

<span data-ttu-id="946b2-131">若要共用交易，內容必須同時共用 `DbConnection` 和 `DbTransaction`。</span><span class="sxs-lookup"><span data-stu-id="946b2-131">To share a transaction, the contexts must share both a `DbConnection` and a `DbTransaction`.</span></span>

### <a name="allow-connection-to-be-externally-provided"></a><span data-ttu-id="946b2-132">允許從外部提供連線</span><span class="sxs-lookup"><span data-stu-id="946b2-132">Allow connection to be externally provided</span></span>

<span data-ttu-id="946b2-133">必須能夠在建構內容時將連線傳遞給內容，才能共用 `DbConnection`。</span><span class="sxs-lookup"><span data-stu-id="946b2-133">Sharing a `DbConnection` requires the ability to pass a connection into a context when constructing it.</span></span>

<span data-ttu-id="946b2-134">若要允許從外部提供 `DbConnection`，最簡單的方式就是停止使用 `DbContext.OnConfiguring` 方法來設定內容，然後從外部建立 `DbContextOptions` 並將其傳遞給內容建構函式。</span><span class="sxs-lookup"><span data-stu-id="946b2-134">The easiest way to allow `DbConnection` to be externally provided, is to stop using the `DbContext.OnConfiguring` method to configure the context and externally create `DbContextOptions` and pass them to the context constructor.</span></span>

> [!TIP]
> <span data-ttu-id="946b2-135">`DbContextOptionsBuilder` 是您在 `DbContext.OnConfiguring` 中用來設定內容的 API，現在您將從外部使用它來建立 `DbContextOptions`。</span><span class="sxs-lookup"><span data-stu-id="946b2-135">`DbContextOptionsBuilder` is the API you used in `DbContext.OnConfiguring` to configure the context, you are now going to use it externally to create `DbContextOptions`.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/SharingTransaction.cs?name=Context&highlight=3,4,5)]

<span data-ttu-id="946b2-136">替代方法是繼續使用 `DbContext.OnConfiguring`，但接受 `DbConnection`，這會在儲存後於 `DbContext.OnConfiguring` 中使用。</span><span class="sxs-lookup"><span data-stu-id="946b2-136">An alternative is to keep using `DbContext.OnConfiguring`, but accept a `DbConnection` that is saved and then used in `DbContext.OnConfiguring`.</span></span>

```csharp
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

### <a name="share-connection-and-transaction"></a><span data-ttu-id="946b2-137">共用連線和交易</span><span class="sxs-lookup"><span data-stu-id="946b2-137">Share connection and transaction</span></span>

<span data-ttu-id="946b2-138">您現在可以建立多個共用相同連線的內容執行個體。</span><span class="sxs-lookup"><span data-stu-id="946b2-138">You can now create multiple context instances that share the same connection.</span></span> <span data-ttu-id="946b2-139">然後使用 `DbContext.Database.UseTransaction(DbTransaction)` API 將兩個內容都登錄在同一個交易中。</span><span class="sxs-lookup"><span data-stu-id="946b2-139">Then use the `DbContext.Database.UseTransaction(DbTransaction)` API to enlist both contexts in the same transaction.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/SharingTransaction.cs?name=Transaction&highlight=1-3,6,14,21-23)]

## <a name="using-external-dbtransactions-relational-databases-only"></a><span data-ttu-id="946b2-140">使用外部 DbTransactions (僅適用於關聯式資料庫)</span><span class="sxs-lookup"><span data-stu-id="946b2-140">Using external DbTransactions (relational databases only)</span></span>

<span data-ttu-id="946b2-141">如果您使用多個資料存取技術來存取關聯式資料庫，則可能會想要在這些不同技術所執行的作業之間共用交易。</span><span class="sxs-lookup"><span data-stu-id="946b2-141">If you are using multiple data access technologies to access a relational database, you may want to share a transaction between operations performed by these different technologies.</span></span>

<span data-ttu-id="946b2-142">下列範例示範如何在同一個交易中執行 ADO.NET SqlClient 作業和 Entity Framework Core 作業。</span><span class="sxs-lookup"><span data-stu-id="946b2-142">The following example, shows how to perform an ADO.NET SqlClient operation and an Entity Framework Core operation in the same transaction.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/ExternalDbTransaction.cs?name=Transaction&highlight=4,9,20,25-27)]

## <a name="using-systemtransactions"></a><span data-ttu-id="946b2-143">使用 System.Transactions</span><span class="sxs-lookup"><span data-stu-id="946b2-143">Using System.Transactions</span></span>

<span data-ttu-id="946b2-144">如果您需要跨較大的範圍進行協調，可以使用環境交易。</span><span class="sxs-lookup"><span data-stu-id="946b2-144">It is possible to use ambient transactions if you need to coordinate across a larger scope.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/AmbientTransaction.cs?name=Transaction&highlight=1,2,3,26-28)]

<span data-ttu-id="946b2-145">此外，也可以登錄在明確交易中。</span><span class="sxs-lookup"><span data-stu-id="946b2-145">It is also possible to enlist in an explicit transaction.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/CommitableTransaction.cs?name=Transaction&highlight=1-2,15,28-30)]

### <a name="limitations-of-systemtransactions"></a><span data-ttu-id="946b2-146">System.Transactions 的限制</span><span class="sxs-lookup"><span data-stu-id="946b2-146">Limitations of System.Transactions</span></span>

1. <span data-ttu-id="946b2-147">EF Core 需倚賴資料庫提供者實作對 System.Transactions 的支援。</span><span class="sxs-lookup"><span data-stu-id="946b2-147">EF Core relies on database providers to implement support for System.Transactions.</span></span> <span data-ttu-id="946b2-148">如果提供者未實作對 System.Transactions 的支援，則對這些 API 發出的呼叫將可能完全被忽略。</span><span class="sxs-lookup"><span data-stu-id="946b2-148">If a provider does not implement support for System.Transactions, it is possible that calls to these APIs will be completely ignored.</span></span> <span data-ttu-id="946b2-149">SqlClient 支援。</span><span class="sxs-lookup"><span data-stu-id="946b2-149">SqlClient supports it.</span></span>

   > [!IMPORTANT]
   > <span data-ttu-id="946b2-150">建議您先測試該 API 是否可與您的提供者正確搭配運作，再倚賴它來管理交易。</span><span class="sxs-lookup"><span data-stu-id="946b2-150">It is recommended that you test that the API behaves correctly with your provider before you rely on it for managing transactions.</span></span> <span data-ttu-id="946b2-151">如果無法正確搭配運作，建議您與資料庫提供者的維護人員連絡。</span><span class="sxs-lookup"><span data-stu-id="946b2-151">You are encouraged to contact the maintainer of the database provider if it does not.</span></span>

2. <span data-ttu-id="946b2-152">從 .NET Core 2.1 的觀點來看，System.object 的執行不包含對分散式交易的支援，因此您無法使用 `TransactionScope` 或 `CommittableTransaction` 協調多個資源管理員的交易。</span><span class="sxs-lookup"><span data-stu-id="946b2-152">As of .NET Core 2.1, the System.Transactions implementation does not include support for distributed transactions, therefore you cannot use `TransactionScope` or `CommittableTransaction` to coordinate transactions across multiple resource managers.</span></span>
