---
title: 攔截器-EF Core
description: 攔截資料庫作業和其他事件
author: ajcvickers
ms.date: 10/08/2020
uid: core/logging-events-diagnostics/interceptors
ms.openlocfilehash: fba9f3d02b8cf504c2cadca8eb844cd3e818e915
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635805"
---
# <a name="interceptors"></a><span data-ttu-id="59b38-103">攔截器</span><span class="sxs-lookup"><span data-stu-id="59b38-103">Interceptors</span></span>

<span data-ttu-id="59b38-104">Entity Framework Core (EF Core) 攔截器可以攔截、修改及/或隱藏 EF Core 作業。</span><span class="sxs-lookup"><span data-stu-id="59b38-104">Entity Framework Core (EF Core) interceptors enable interception, modification, and/or suppression of EF Core operations.</span></span> <span data-ttu-id="59b38-105">這包括執行命令等低層級的資料庫作業，以及較高層級的作業，例如對 SaveChanges 的呼叫。</span><span class="sxs-lookup"><span data-stu-id="59b38-105">This includes low-level database operations such as executing a command, as well as higher-level operations, such as calls to SaveChanges.</span></span>

<span data-ttu-id="59b38-106">攔截器與記錄和診斷不同，因為它們允許修改或隱藏正在攔截的作業。</span><span class="sxs-lookup"><span data-stu-id="59b38-106">Interceptors are different from logging and diagnostics in that they allow modification or suppression of the operation being intercepted.</span></span> <span data-ttu-id="59b38-107">[簡易記錄](xref:core/logging-events-diagnostics/simple-logging) 或 [Microsoft Extensions。記錄](xref:core/logging-events-diagnostics/extensions-logging) 是較佳的記錄選擇。</span><span class="sxs-lookup"><span data-stu-id="59b38-107">[Simple logging](xref:core/logging-events-diagnostics/simple-logging) or [Microsoft.Extensions.Logging](xref:core/logging-events-diagnostics/extensions-logging) are better choices for logging.</span></span>

<span data-ttu-id="59b38-108">當設定內容時，會針對每個 DbCoNtext 實例註冊攔截器。</span><span class="sxs-lookup"><span data-stu-id="59b38-108">Interceptors are registered per DbContext instance when the context is configured.</span></span> <span data-ttu-id="59b38-109">您可以使用 [診斷](xref:core/logging-events-diagnostics/diagnostic-listeners) 接聽程式來取得相同資訊，但會針對進程中的所有 DbCoNtext 實例取得相同的資訊。</span><span class="sxs-lookup"><span data-stu-id="59b38-109">Use a [diagnostic listener](xref:core/logging-events-diagnostics/diagnostic-listeners) to get the same information but for all DbContext instances in the process.</span></span>

## <a name="registering-interceptors"></a><span data-ttu-id="59b38-110">註冊攔截器</span><span class="sxs-lookup"><span data-stu-id="59b38-110">Registering interceptors</span></span>

<span data-ttu-id="59b38-111">設定 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.AddInterceptors%2A> [DbCoNtext 實例](xref:core/dbcontext-configuration/index)時，會使用註冊攔截器。</span><span class="sxs-lookup"><span data-stu-id="59b38-111">Interceptors are registered using <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.AddInterceptors%2A> when [configuring a DbContext instance](xref:core/dbcontext-configuration/index).</span></span> <span data-ttu-id="59b38-112">這通常是在的覆寫中完成 <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="59b38-112">This is commonly done in an override of <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="59b38-113">例如：</span><span class="sxs-lookup"><span data-stu-id="59b38-113">For example:</span></span>

<!--
public class ExampleContext : BlogsContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder) 
        => optionsBuilder.AddInterceptors(new TaggedQueryCommandInterceptor());
}
-->
[!code-csharp[RegisterInterceptor](../../../samples/core/Miscellaneous/CommandInterception/Program.cs?name=RegisterInterceptor)]

<span data-ttu-id="59b38-114">或者，您也 `AddInterceptors` 可以在 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> 建立 <xref:Microsoft.EntityFrameworkCore.DbContextOptions> 實例以傳遞至 DbCoNtext 的函式時，將其當作的一部分來呼叫。</span><span class="sxs-lookup"><span data-stu-id="59b38-114">Alternately, `AddInterceptors` can be called as part of <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> or when creating a <xref:Microsoft.EntityFrameworkCore.DbContextOptions> instance to pass to the DbContext constructor.</span></span>

> [!TIP]
> <span data-ttu-id="59b38-115">當使用 AddDbCoNtext 或將 DbCoNtextOptions 實例傳遞至 DbCoNtext 的函式時，仍會呼叫 OnConfiguring。</span><span class="sxs-lookup"><span data-stu-id="59b38-115">OnConfiguring is still called when AddDbContext is used or a DbContextOptions instance is passed to the DbContext constructor.</span></span> <span data-ttu-id="59b38-116">這讓它成為套用內容設定的理想位置，不論 DbCoNtext 的結構為何。</span><span class="sxs-lookup"><span data-stu-id="59b38-116">This makes it the ideal place to apply context configuration regardless of how the DbContext is constructed.</span></span>

<span data-ttu-id="59b38-117">攔截器通常是無狀態的，這表示單一攔截器實例可用於所有 DbCoNtext 實例。</span><span class="sxs-lookup"><span data-stu-id="59b38-117">Interceptors are often stateless, which means that a single interceptor instance can be used for all DbContext instances.</span></span> <span data-ttu-id="59b38-118">例如：</span><span class="sxs-lookup"><span data-stu-id="59b38-118">For example:</span></span>

<!--
public class TaggedQueryCommandInterceptorContext : BlogsContext
{
    private static readonly TaggedQueryCommandInterceptor _interceptor 
        = new TaggedQueryCommandInterceptor();

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder) 
        => optionsBuilder.AddInterceptors(_interceptor);
}
-->
[!code-csharp[RegisterStatelessInterceptor](../../../samples/core/Miscellaneous/CommandInterception/Program.cs?name=RegisterStatelessInterceptor)]

<span data-ttu-id="59b38-119">每個攔截器實例都必須執行一或多個衍生自的介面 <xref:Microsoft.EntityFrameworkCore.Diagnostics.IInterceptor> 。</span><span class="sxs-lookup"><span data-stu-id="59b38-119">Every interceptor instance must implement one or more interface derived from <xref:Microsoft.EntityFrameworkCore.Diagnostics.IInterceptor>.</span></span> <span data-ttu-id="59b38-120">每個實例都應該只註冊一次，即使它會執行多個攔截介面;EF Core 會適當地路由每個介面的事件。</span><span class="sxs-lookup"><span data-stu-id="59b38-120">Each instance should only be registered once even if it implements multiple interception interfaces; EF Core will route events for each interface as appropriate.</span></span>

## <a name="database-interception"></a><span data-ttu-id="59b38-121">資料庫攔截</span><span class="sxs-lookup"><span data-stu-id="59b38-121">Database interception</span></span>

> [!NOTE]
> <span data-ttu-id="59b38-122">資料庫攔截是在 EF Core 3.0 中引進，而且僅適用于關係資料庫提供者。</span><span class="sxs-lookup"><span data-stu-id="59b38-122">Database interception was introduced in EF Core 3.0 and is only available for relational database providers.</span></span>
> <span data-ttu-id="59b38-123">EF Core 5.0 中引進了儲存點支援。</span><span class="sxs-lookup"><span data-stu-id="59b38-123">Savepoint support was introduced in EF Core 5.0.</span></span>

<span data-ttu-id="59b38-124">低層級資料庫攔截會分割成下表所示的三個介面。</span><span class="sxs-lookup"><span data-stu-id="59b38-124">Low-level database interception is split into the three interfaces shown in the following table.</span></span>

| <span data-ttu-id="59b38-125">攔截 器</span><span class="sxs-lookup"><span data-stu-id="59b38-125">Interceptor</span></span>                                                            | <span data-ttu-id="59b38-126">資料庫作業遭到攔截</span><span class="sxs-lookup"><span data-stu-id="59b38-126">Database operations intercepted</span></span>
|:-----------------------------------------------------------------------|-------------------------------------------------
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbCommandInterceptor> | <span data-ttu-id="59b38-127">建立命令</span><span class="sxs-lookup"><span data-stu-id="59b38-127">Creating commands</span></span></br><span data-ttu-id="59b38-128">執行命令</span><span class="sxs-lookup"><span data-stu-id="59b38-128">Executing commands</span></span></br><span data-ttu-id="59b38-129">命令失敗</span><span class="sxs-lookup"><span data-stu-id="59b38-129">Command failures</span></span></br><span data-ttu-id="59b38-130">處置命令的 DbDataReader</span><span class="sxs-lookup"><span data-stu-id="59b38-130">Disposing the command's DbDataReader</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbConnectionInterceptor> | <span data-ttu-id="59b38-131">開啟和關閉連接</span><span class="sxs-lookup"><span data-stu-id="59b38-131">Opening and closing connections</span></span></br><span data-ttu-id="59b38-132">連接失敗</span><span class="sxs-lookup"><span data-stu-id="59b38-132">Connection failures</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbTransactionInterceptor> | <span data-ttu-id="59b38-133">建立交易</span><span class="sxs-lookup"><span data-stu-id="59b38-133">Creating transactions</span></span></br><span data-ttu-id="59b38-134">使用現有的交易</span><span class="sxs-lookup"><span data-stu-id="59b38-134">Using existing transactions</span></span></br><span data-ttu-id="59b38-135">認可交易</span><span class="sxs-lookup"><span data-stu-id="59b38-135">Committing transactions</span></span></br><span data-ttu-id="59b38-136">回復交易</span><span class="sxs-lookup"><span data-stu-id="59b38-136">Rolling back transactions</span></span></br><span data-ttu-id="59b38-137">建立和使用儲存點</span><span class="sxs-lookup"><span data-stu-id="59b38-137">Creating and using savepoints</span></span></br><span data-ttu-id="59b38-138">交易失敗</span><span class="sxs-lookup"><span data-stu-id="59b38-138">Transaction failures</span></span>

<span data-ttu-id="59b38-139">基類 <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor> 、 <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbConnectionInterceptor> 和 <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbTransactionInterceptor> 包含對應介面中每個方法的無作業執行。</span><span class="sxs-lookup"><span data-stu-id="59b38-139">The base classes <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor>, <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbConnectionInterceptor>, and <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbTransactionInterceptor> contain no-op implementations for each method in the corresponding interface.</span></span> <span data-ttu-id="59b38-140">使用基類，以避免需要執行未使用的攔截方法。</span><span class="sxs-lookup"><span data-stu-id="59b38-140">Use the base classes to avoid the need to implement unused interception methods.</span></span>

<span data-ttu-id="59b38-141">每個攔截器類型上的方法都是成對的，第一次是在資料庫作業啟動之前呼叫，第二個則是在作業完成之後呼叫。</span><span class="sxs-lookup"><span data-stu-id="59b38-141">The methods on each interceptor type come in pairs, with the first being called before the database operation is started, and the second after the operation has completed.</span></span> <span data-ttu-id="59b38-142">例如，</span><span class="sxs-lookup"><span data-stu-id="59b38-142">For example.</span></span> <span data-ttu-id="59b38-143">例如，在 <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor.ReaderExecuting%2A?displayProperty=nameWithType> 執行查詢之前呼叫，並 <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor.ReaderExecuted%2A?displayProperty=nameWithType> 在查詢傳送至資料庫之後呼叫。</span><span class="sxs-lookup"><span data-stu-id="59b38-143">For example, <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor.ReaderExecuting%2A?displayProperty=nameWithType> is called before a query is executed, and <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor.ReaderExecuted%2A?displayProperty=nameWithType> is called after query has been sent to the database.</span></span>

<span data-ttu-id="59b38-144">每一對方法都有同步和非同步變化。</span><span class="sxs-lookup"><span data-stu-id="59b38-144">Each pair of methods have both sync and async variations.</span></span> <span data-ttu-id="59b38-145">這可讓非同步 i/o （例如要求存取權杖）在攔截非同步資料庫作業時發生。</span><span class="sxs-lookup"><span data-stu-id="59b38-145">This allows for asynchronous I/O, such as requesting an access token, to happen as part of intercepting an async database operation.</span></span>

### <a name="example-command-interception-to-add-query-hints"></a><span data-ttu-id="59b38-146">範例：命令攔截以新增查詢提示</span><span class="sxs-lookup"><span data-stu-id="59b38-146">Example: Command interception to add query hints</span></span>

> [!TIP]  
> <span data-ttu-id="59b38-147">您可以從 GitHub [下載命令攔截器範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/CommandInterception) 。</span><span class="sxs-lookup"><span data-stu-id="59b38-147">You can [download the command interceptor sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/CommandInterception) from GitHub.</span></span>

<span data-ttu-id="59b38-148"><xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbCommandInterceptor>可以用來修改 SQL，然後再傳送至資料庫。</span><span class="sxs-lookup"><span data-stu-id="59b38-148">An <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbCommandInterceptor> can be used to modify SQL before it is sent to the database.</span></span> <span data-ttu-id="59b38-149">此範例顯示如何修改 SQL 以包含查詢提示。</span><span class="sxs-lookup"><span data-stu-id="59b38-149">This example shows how to modify the SQL to include a query hint.</span></span>

<span data-ttu-id="59b38-150">攔截的最棘手部分通常會判斷命令是否對應到需要修改的查詢。</span><span class="sxs-lookup"><span data-stu-id="59b38-150">Often, the trickiest part of the interception is determining when the command corresponds to the query that needs to be modified.</span></span> <span data-ttu-id="59b38-151">剖析 SQL 是一個選項，但通常是脆弱的。</span><span class="sxs-lookup"><span data-stu-id="59b38-151">Parsing the SQL is one option, but tends to be fragile.</span></span> <span data-ttu-id="59b38-152">另一個選項是使用 [EF Core 的查詢標記](xref:core/querying/tags) 來標記每個應該修改的查詢。</span><span class="sxs-lookup"><span data-stu-id="59b38-152">Another option is to use [EF Core query tags](xref:core/querying/tags) to tag each query that should be modified.</span></span> <span data-ttu-id="59b38-153">例如：</span><span class="sxs-lookup"><span data-stu-id="59b38-153">For example:</span></span>

<!--
            var blogs1 = context.Blogs.TagWith("Use hint: robust plan").ToList();
-->
[!code-csharp[TaggedQuery](../../../samples/core/Miscellaneous/CommandInterception/Program.cs?name=TaggedQuery)]

<span data-ttu-id="59b38-154">然後可以在攔截器中偵測到這個標記，因為它一律會以批註的形式包含在命令文字的第一行中。</span><span class="sxs-lookup"><span data-stu-id="59b38-154">This tag can then be detected in the interceptor as it will always be included as a comment in the first line of the command text.</span></span> <span data-ttu-id="59b38-155">偵測到標記時，會修改查詢 SQL 以加入適當的提示：</span><span class="sxs-lookup"><span data-stu-id="59b38-155">On detecting the tag, the query SQL is modified to add the appropriate hint:</span></span>

<!--
public class TaggedQueryCommandInterceptor : DbCommandInterceptor
{
    public override InterceptionResult<DbDataReader> ReaderExecuting(
        DbCommand command,
        CommandEventData eventData,
        InterceptionResult<DbDataReader> result)
    {
        ManipulateCommand(command);

        return result;
    }

    public override ValueTask<InterceptionResult<DbDataReader>> ReaderExecutingAsync(
        DbCommand command,
        CommandEventData eventData,
        InterceptionResult<DbDataReader> result,
        CancellationToken cancellationToken = default)
    {
        ManipulateCommand(command);

        return new ValueTask<InterceptionResult<DbDataReader>>(result);
    }

    private static void ManipulateCommand(DbCommand command)
    {
        if (command.CommandText.StartsWith("-- Use hint: robust plan", StringComparison.Ordinal))
        {
            command.CommandText += " OPTION (ROBUST PLAN)";
        }
    }
}
-->
[!code-csharp[TaggedQueryCommandInterceptor](../../../samples/core/Miscellaneous/CommandInterception/TaggedQueryCommandInterceptor.cs?name=TaggedQueryCommandInterceptor)]

<span data-ttu-id="59b38-156">注意：</span><span class="sxs-lookup"><span data-stu-id="59b38-156">Notice:</span></span>

* <span data-ttu-id="59b38-157">攔截器會繼承自 <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor> ，以避免必須在攔截器介面中執行每個方法。</span><span class="sxs-lookup"><span data-stu-id="59b38-157">The interceptor inherits from <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor> to avoid having to implement every method in the interceptor interface.</span></span>
* <span data-ttu-id="59b38-158">攔截器會同時實行同步處理和非同步方法。</span><span class="sxs-lookup"><span data-stu-id="59b38-158">The interceptor implements both sync and async methods.</span></span> <span data-ttu-id="59b38-159">這可確保相同的查詢提示會套用到同步和非同步查詢。</span><span class="sxs-lookup"><span data-stu-id="59b38-159">This ensures that the same query hint is applied to sync and async queries.</span></span>
* <span data-ttu-id="59b38-160">攔截器會在 `Executing` 傳送至資料庫 _之前，先_ 在產生的 SQL 中，執行 EF Core 所呼叫的方法。</span><span class="sxs-lookup"><span data-stu-id="59b38-160">The interceptor implements the `Executing` methods which are called by EF Core with the generated SQL _before_ it is sent to the database.</span></span> <span data-ttu-id="59b38-161">將這與在 `Executed` 資料庫呼叫傳回之後呼叫的方法做對比。</span><span class="sxs-lookup"><span data-stu-id="59b38-161">Contrast this with the `Executed` methods, which are called after the database call has returned.</span></span>

<span data-ttu-id="59b38-162">執行此範例中的程式碼時，會在標記查詢時產生下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="59b38-162">Running the code in this example generates the following when a query is tagged:</span></span>

```sql
-- Use hint: robust plan

SELECT [b].[Id], [b].[Name]
FROM [Blogs] AS [b] OPTION (ROBUST PLAN)
```

<span data-ttu-id="59b38-163">另一方面，當查詢未加上標籤時，就會將它傳送至未修改的資料庫：</span><span class="sxs-lookup"><span data-stu-id="59b38-163">On the other hand, when a query is not tagged, then it is sent to the database unmodified:</span></span>

```sql
SELECT [b].[Id], [b].[Name]
FROM [Blogs] AS [b]
```

### <a name="example-connection-interception-for-sql-azure-authentication-using-add"></a><span data-ttu-id="59b38-164">範例：使用 ADD 的 SQL Azure 驗證連接攔截</span><span class="sxs-lookup"><span data-stu-id="59b38-164">Example: Connection interception for SQL Azure authentication using ADD</span></span>

> [!TIP]  
> <span data-ttu-id="59b38-165">您可以從 GitHub [下載連接攔截器範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/ConnectionInterception) 。</span><span class="sxs-lookup"><span data-stu-id="59b38-165">You can [download the connection interceptor sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/ConnectionInterception) from GitHub.</span></span>

<span data-ttu-id="59b38-166"><xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbConnectionInterceptor>可以用來在 <xref:System.Data.Common.DbConnection> 用來連接到資料庫之前，先操作。</span><span class="sxs-lookup"><span data-stu-id="59b38-166">An <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbConnectionInterceptor> can be used to manipulate the <xref:System.Data.Common.DbConnection> before it is used to connect to the database.</span></span> <span data-ttu-id="59b38-167">這可以用來取得 Azure Active Directory (AAD) 存取權杖。</span><span class="sxs-lookup"><span data-stu-id="59b38-167">This can be used to obtain an Azure Active Directory (AAD) access token.</span></span> <span data-ttu-id="59b38-168">例如：</span><span class="sxs-lookup"><span data-stu-id="59b38-168">For example:</span></span>

<!--
public class AadAuthenticationInterceptor : DbConnectionInterceptor
{
    public override InterceptionResult ConnectionOpening(
        DbConnection connection,
        ConnectionEventData eventData,
        InterceptionResult result)
        => throw new InvalidOperationException("Open connections asynchronously when using AAD authentication.");

    public override async ValueTask<InterceptionResult> ConnectionOpeningAsync(
        DbConnection connection,
        ConnectionEventData eventData,
        InterceptionResult result,
        CancellationToken cancellationToken = default)
    {
        var sqlConnection = (SqlConnection)connection;

        var provider = new AzureServiceTokenProvider();
        // Note: in some situations the access token may not be cached automatically the Azure Token Provider.
        // Depending on the kind of token requested, you may need to implement your own caching here.
        sqlConnection.AccessToken = await provider.GetAccessTokenAsync("https://database.windows.net/", null, cancellationToken);

        return result;
    }
}
-->
[!code-csharp[AadAuthenticationInterceptor](../../../samples/core/Miscellaneous/ConnectionInterception/AadAuthenticationInterceptor.cs?name=AadAuthenticationInterceptor)]

> [!TIP]
> <span data-ttu-id="59b38-169">[SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient/) 現在透過連接字串支援 AAD 驗證。</span><span class="sxs-lookup"><span data-stu-id="59b38-169">[Microsoft.Data.SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient/) now supports AAD authentication via connection string.</span></span> <span data-ttu-id="59b38-170">如需相關資訊，請參閱 <xref:Microsoft.Data.SqlClient.SqlAuthenticationMethod> 。</span><span class="sxs-lookup"><span data-stu-id="59b38-170">See <xref:Microsoft.Data.SqlClient.SqlAuthenticationMethod> for more information.</span></span>

> [!WARNING]
> <span data-ttu-id="59b38-171">請注意，如果進行同步呼叫以開啟連接，攔截器就會擲回。</span><span class="sxs-lookup"><span data-stu-id="59b38-171">Notice that the interceptor throws if a sync call is made to open the connection.</span></span> <span data-ttu-id="59b38-172">這是因為沒有任何非非同步方法可以取得存取權杖，而且沒有 [任何通用且簡單的方法可從非非同步內容呼叫非同步方法，而不會產生風險鎖死](https://devblogs.microsoft.com/dotnet/configureawait-faq/)。</span><span class="sxs-lookup"><span data-stu-id="59b38-172">This is because there is no non-async method to obtain the access token and there is [no universal and simple way to call an async method from non-async context without risking deadlock](https://devblogs.microsoft.com/dotnet/configureawait-faq/).</span></span>

> [!WARNING]
> <span data-ttu-id="59b38-173">在某些情況下，可能不會自動快取 Azure 權杖提供者的存取權杖。</span><span class="sxs-lookup"><span data-stu-id="59b38-173">in some situations the access token may not be cached automatically the Azure Token Provider.</span></span> <span data-ttu-id="59b38-174">視所要求的權杖類型而定，您可能需要在此執行自己的快取。</span><span class="sxs-lookup"><span data-stu-id="59b38-174">Depending on the kind of token requested, you may need to implement your own caching here.</span></span>

### <a name="example-advanced-command-interception-for-caching"></a><span data-ttu-id="59b38-175">範例：快取的 Advanced 命令攔截</span><span class="sxs-lookup"><span data-stu-id="59b38-175">Example: Advanced command interception for caching</span></span>

> [!TIP]  
> <span data-ttu-id="59b38-176">您可以從 GitHub [下載 advanced command 攔截器範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/CachingInterception) 。</span><span class="sxs-lookup"><span data-stu-id="59b38-176">You can [download the advanced command interceptor sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/CachingInterception) from GitHub.</span></span>

<span data-ttu-id="59b38-177">EF Core 攔截器可以：</span><span class="sxs-lookup"><span data-stu-id="59b38-177">EF Core interceptors can:</span></span>

* <span data-ttu-id="59b38-178">告訴 EF Core 隱藏執行正在攔截的作業</span><span class="sxs-lookup"><span data-stu-id="59b38-178">Tell EF Core to suppress executing the operation being intercepted</span></span>
* <span data-ttu-id="59b38-179">將回報的作業結果變更回 EF Core</span><span class="sxs-lookup"><span data-stu-id="59b38-179">Change the result of the operation reported back to EF Core</span></span>

<span data-ttu-id="59b38-180">此範例顯示使用這些功能的攔截器，其行為就像基本的第二層快取。</span><span class="sxs-lookup"><span data-stu-id="59b38-180">This example shows an interceptor that uses these features to behave like a primitive second-level cache.</span></span> <span data-ttu-id="59b38-181">針對特定的查詢會傳回快取的查詢結果，以避免資料庫往返。</span><span class="sxs-lookup"><span data-stu-id="59b38-181">Cached query results are returned for a specific query, avoiding a database roundtrip.</span></span>

> [!WARNING]
> <span data-ttu-id="59b38-182">以這種方式變更 EF Core 預設行為時，請小心。</span><span class="sxs-lookup"><span data-stu-id="59b38-182">Take care when changing the EF Core default behavior in this way.</span></span> <span data-ttu-id="59b38-183">如果發生無法正確處理的異常結果，EF Core 可能會以非預期的方式表現。</span><span class="sxs-lookup"><span data-stu-id="59b38-183">EF Core may behave in unexpected ways if it gets an abnormal result that it cannot process correctly.</span></span> <span data-ttu-id="59b38-184">此外，此範例會示範攔截器概念;它不適合作為健全的第二層快取執行的範本。</span><span class="sxs-lookup"><span data-stu-id="59b38-184">Also, this example demonstrates interceptor concepts; it is not intended as a template for a robust second-level cache implementation.</span></span>

<span data-ttu-id="59b38-185">在此範例中，應用程式會經常執行查詢來取得最新的「每日訊息」：</span><span class="sxs-lookup"><span data-stu-id="59b38-185">In this example, the application frequently executes a query to obtain the most recent "daily message":</span></span>

<!--
        async Task<string> GetDailyMessage(DailyMessageContext context)
            => (await context.DailyMessages.TagWith("Get_Daily_Message").OrderBy(e => e.Id).LastAsync()).Message;
-->
[!code-csharp[GetDailyMessage](../../../samples/core/Miscellaneous/CachingInterception/Program.cs?name=GetDailyMessage)]

<span data-ttu-id="59b38-186">此查詢已 [標記](xref:core/querying/tags) 為可在攔截器中輕鬆地偵測到。</span><span class="sxs-lookup"><span data-stu-id="59b38-186">This query is [tagged](xref:core/querying/tags) so that it can be easily detected in the interceptor.</span></span> <span data-ttu-id="59b38-187">其構想是每天只查詢一次新訊息的資料庫。</span><span class="sxs-lookup"><span data-stu-id="59b38-187">The idea is to only query the database for a new message once every day.</span></span> <span data-ttu-id="59b38-188">有時候，應用程式會使用快取的結果。</span><span class="sxs-lookup"><span data-stu-id="59b38-188">At other times the application will use a cached result.</span></span> <span data-ttu-id="59b38-189"> (範例會在範例中使用10秒的延遲，以模擬新的一天。 ) </span><span class="sxs-lookup"><span data-stu-id="59b38-189">(The sample uses delay of 10 seconds in the sample to simulate a new day.)</span></span>

#### <a name="interceptor-state"></a><span data-ttu-id="59b38-190">攔截器狀態</span><span class="sxs-lookup"><span data-stu-id="59b38-190">Interceptor state</span></span>

<span data-ttu-id="59b38-191">此攔截器是具狀態的：它會儲存最近查詢過的每日訊息的識別碼和郵件內文，以及執行該查詢的時間。</span><span class="sxs-lookup"><span data-stu-id="59b38-191">This interceptor is stateful: it stores the ID and message text of the most recent daily message queried, plus the time when that query was executed.</span></span> <span data-ttu-id="59b38-192">因為此狀態，所以我們也需要 [鎖定](/dotnet/csharp/language-reference/keywords/lock-statement) ，因為快取需要相同的攔截器必須由多個內容實例使用。</span><span class="sxs-lookup"><span data-stu-id="59b38-192">Because of this state we also need a [lock](/dotnet/csharp/language-reference/keywords/lock-statement) since the caching requires that same interceptor must be used by multiple context instances.</span></span>

<!--
    private readonly object _lock = new object();
    private int _id;
    private string _message;
    private DateTime _queriedAt;
-->
[!code-csharp[InterceptorState](../../../samples/core/Miscellaneous/CachingInterception/CachingCommandInterceptor.cs?name=InterceptorState)]

#### <a name="before-execution"></a><span data-ttu-id="59b38-193">執行前</span><span class="sxs-lookup"><span data-stu-id="59b38-193">Before execution</span></span>

<span data-ttu-id="59b38-194">在 `Executing` 方法 (亦即進行資料庫呼叫之前) ，攔截器會偵測已標記的查詢，然後檢查是否有快取的結果。</span><span class="sxs-lookup"><span data-stu-id="59b38-194">In the `Executing` method (i.e. before making a database call), the interceptor detects the tagged query and then checks if there is a cached result.</span></span> <span data-ttu-id="59b38-195">如果找到這樣的結果，則會抑制查詢並改用快取的結果。</span><span class="sxs-lookup"><span data-stu-id="59b38-195">If such a result is found, then the query is suppressed and cached results are used instead.</span></span>

<!--
    public override ValueTask<InterceptionResult<DbDataReader>> ReaderExecutingAsync(
        DbCommand command,
        CommandEventData eventData,
        InterceptionResult<DbDataReader> result,
        CancellationToken cancellationToken = default)
    {
        if (command.CommandText.StartsWith("-- Get_Daily_Message", StringComparison.Ordinal))
        {
            lock (_lock)
            {
                if (_message != null
                    && DateTime.UtcNow < _queriedAt + new TimeSpan(0, 0, 10))
                {
                    command.CommandText = "-- Get_Daily_Message: Skipping DB call; using cache.";
                    result = InterceptionResult<DbDataReader>.SuppressWithResult(new CachedDailyMessageDataReader(_id, _message));
                }
            }
        }

        return new ValueTask<InterceptionResult<DbDataReader>>(result);
    }
-->
[!code-csharp[ReaderExecutingAsync](../../../samples/core/Miscellaneous/CachingInterception/CachingCommandInterceptor.cs?name=ReaderExecutingAsync)]

<span data-ttu-id="59b38-196">請注意程式碼如何呼叫 <xref:Microsoft.EntityFrameworkCore.Diagnostics.InterceptionResult%601.SuppressWithResult%2A?displayProperty=nameWithType> 和傳遞包含快取資料的取代 <xref:System.Data.Common.DbDataReader> 。</span><span class="sxs-lookup"><span data-stu-id="59b38-196">Notice how the code calls <xref:Microsoft.EntityFrameworkCore.Diagnostics.InterceptionResult%601.SuppressWithResult%2A?displayProperty=nameWithType> and passes a replacement <xref:System.Data.Common.DbDataReader> containing the cached data.</span></span> <span data-ttu-id="59b38-197">然後會傳回這個 InterceptionResult，而導致查詢執行隱藏。</span><span class="sxs-lookup"><span data-stu-id="59b38-197">This InterceptionResult is then returned, causing suppression of query execution.</span></span> <span data-ttu-id="59b38-198">EF Core 會使用取代讀取器作為查詢的結果。</span><span class="sxs-lookup"><span data-stu-id="59b38-198">The replacement reader is instead used by EF Core as the results of the query.</span></span>

<span data-ttu-id="59b38-199">此攔截器也會操控命令文字。</span><span class="sxs-lookup"><span data-stu-id="59b38-199">This interceptor also manipulates the command text.</span></span> <span data-ttu-id="59b38-200">這並非必要的操作，但可改善記錄訊息中的清楚明瞭。</span><span class="sxs-lookup"><span data-stu-id="59b38-200">This manipulation is not required, but improves clarity in log messages.</span></span> <span data-ttu-id="59b38-201">因為目前不會執行查詢，所以命令文字不需要是有效的 SQL。</span><span class="sxs-lookup"><span data-stu-id="59b38-201">The command text does not need to be valid SQL since the query is now not going to be executed.</span></span>

#### <a name="after-execution"></a><span data-ttu-id="59b38-202">執行之後</span><span class="sxs-lookup"><span data-stu-id="59b38-202">After execution</span></span>

<span data-ttu-id="59b38-203">如果沒有可用的快取訊息，或已過期，則上述程式碼不會隱藏結果。</span><span class="sxs-lookup"><span data-stu-id="59b38-203">If no cached message is available, or if it has expired, then the code above does not suppress the result.</span></span> <span data-ttu-id="59b38-204">因此 EF Core 會正常執行查詢。</span><span class="sxs-lookup"><span data-stu-id="59b38-204">EF Core will therefore execute the query as normal.</span></span> <span data-ttu-id="59b38-205">然後，它會在執行之後返回攔截器的 `Executed` 方法。</span><span class="sxs-lookup"><span data-stu-id="59b38-205">It will then return to the interceptor's `Executed` method after execution.</span></span> <span data-ttu-id="59b38-206">此時，如果結果不是快取的讀取器，則會從實際讀取器 exacted 新的訊息識別碼和字串，並在下次使用此查詢時加以快取。</span><span class="sxs-lookup"><span data-stu-id="59b38-206">At this point if the result is not already a cached reader, then the new message ID and string is exacted from the real reader and cached ready for the next use of this query.</span></span>

<!--
    public override async ValueTask<DbDataReader> ReaderExecutedAsync(
        DbCommand command,
        CommandExecutedEventData eventData,
        DbDataReader result,
        CancellationToken cancellationToken = default)
    {
        if (command.CommandText.StartsWith("-- Get_Daily_Message", StringComparison.Ordinal)
            && !(result is CachedDailyMessageDataReader))
        {
            try
            {
                await result.ReadAsync(cancellationToken);

                lock (_lock)
                {
                    _id = result.GetInt32(0);
                    _message = result.GetString(1);
                    _queriedAt = DateTime.UtcNow;
                    return new CachedDailyMessageDataReader(_id, message);
                }
            }
            finally
            {
                await result.DisposeAsync();
            }
        }

        return result;
    }
-->
[!code-csharp[ReaderExecutedAsync](../../../samples/core/Miscellaneous/CachingInterception/CachingCommandInterceptor.cs?name=ReaderExecutedAsync)]

#### <a name="demonstration"></a><span data-ttu-id="59b38-207">示範</span><span class="sxs-lookup"><span data-stu-id="59b38-207">Demonstration</span></span>

<span data-ttu-id="59b38-208">快取 [攔截器範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/CachingInterception) 包含一個簡單的主控台應用程式，可查詢每日訊息來測試快取：</span><span class="sxs-lookup"><span data-stu-id="59b38-208">The [caching interceptor sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/CachingInterception) contains a simple console application that queries for daily messages to test the caching:</span></span>

<!--
        // 1. Initialize the database with some daily messages.
        using (var context = new DailyMessageContext())
        {
            await context.Database.EnsureDeletedAsync();
            await context.Database.EnsureCreatedAsync();

            context.AddRange(
                new DailyMessage { Message = "Remember: All builds are GA; no builds are RTM." },
                new DailyMessage { Message = "Keep calm and drink tea" });

            await context.SaveChangesAsync();
        }

        // 2. Query for the most recent daily message. It will be cached for 10 seconds.
        using (var context = new DailyMessageContext())
        {
            Console.WriteLine(await GetDailyMessage(context));
        }

        // 3. Insert a new daily message.
        using (var context = new DailyMessageContext())
        {
            context.Add(new DailyMessage { Message = "Free beer for unicorns" });

            await context.SaveChangesAsync();
        }

        // 4. Cached message is used until cache expires.
        using (var context = new DailyMessageContext())
        {
            Console.WriteLine(await GetDailyMessage(context));
        }

        // 5. Pretend it's the next day.
        Thread.Sleep(10000);

        // 6. Cache is expired, so the last message will noe be queried again.
        using (var context = new DailyMessageContext())
        {
            Console.WriteLine(await GetDailyMessage(context));
        }
        
        #region GetDailyMessage
        async Task<string> GetDailyMessage(DailyMessageContext context)
            => (await context.DailyMessages.TagWith("Get_Daily_Message").OrderBy(e => e.Id).LastAsync()).Message;
        #endregion
-->
[!code-csharp[Main](../../../samples/core/Miscellaneous/CachingInterception/Program.cs?name=Main)]

<span data-ttu-id="59b38-209">這會導致下列輸出：</span><span class="sxs-lookup"><span data-stu-id="59b38-209">This results in the following output:</span></span>

```output
info: 10/15/2020 12:32:11.801 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      -- Get_Daily_Message

      SELECT "d"."Id", "d"."Message"
      FROM "DailyMessages" AS "d"
      ORDER BY "d"."Id" DESC
      LIMIT 1

Keep calm and drink tea

info: 10/15/2020 12:32:11.821 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (0ms) [Parameters=[@p0='Free beer for unicorns' (Size = 22)], CommandType='Text', CommandTimeout='30']
      INSERT INTO "DailyMessages" ("Message")
      VALUES (@p0);
      SELECT "Id"
      FROM "DailyMessages"
      WHERE changes() = 1 AND "rowid" = last_insert_rowid();

info: 10/15/2020 12:32:11.826 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      -- Get_Daily_Message: Skipping DB call; using cache.

Keep calm and drink tea

info: 10/15/2020 12:32:21.833 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      -- Get_Daily_Message

      SELECT "d"."Id", "d"."Message"
      FROM "DailyMessages" AS "d"
      ORDER BY "d"."Id" DESC
      LIMIT 1

Free beer for unicorns
```

<span data-ttu-id="59b38-210">請注意，在記錄檔輸出中，應用程式會繼續使用快取的訊息，直到超時過期為止，此時會重新查詢資料庫中是否有任何新訊息。</span><span class="sxs-lookup"><span data-stu-id="59b38-210">Notice from the log output that the application continues to use the cached message until the timeout expires, at which point the database is queried again for any new message.</span></span>

## <a name="savechanges-interception"></a><span data-ttu-id="59b38-211">SaveChanges 攔截</span><span class="sxs-lookup"><span data-stu-id="59b38-211">SaveChanges interception</span></span>

> [!NOTE]
> <span data-ttu-id="59b38-212">SaveChanges 攔截是在 EF Core 5.0 中引進。</span><span class="sxs-lookup"><span data-stu-id="59b38-212">SaveChanges interception was introduced in EF Core 5.0.</span></span>

> [!TIP]  
> <span data-ttu-id="59b38-213">您可以從 GitHub [下載 SaveChanges 攔截器範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SaveChangesInterception) 。</span><span class="sxs-lookup"><span data-stu-id="59b38-213">You can [download the SaveChanges interceptor sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SaveChangesInterception) from GitHub.</span></span>

<span data-ttu-id="59b38-214"><xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> 和 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A> 攔截點是由介面所定義 <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor> 。</span><span class="sxs-lookup"><span data-stu-id="59b38-214"><xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> and <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A> interception points are defined by the <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor> interface.</span></span> <span data-ttu-id="59b38-215">針對其他攔截器，則 <xref:Microsoft.EntityFrameworkCore.Diagnostics.SaveChangesInterceptor> 會提供具有無 op 方法的基類，以方便使用。</span><span class="sxs-lookup"><span data-stu-id="59b38-215">As for other interceptors, the <xref:Microsoft.EntityFrameworkCore.Diagnostics.SaveChangesInterceptor> base class with no-op methods is provided as a convenience.</span></span>

> [!TIP]
> <span data-ttu-id="59b38-216">攔截器功能強大。</span><span class="sxs-lookup"><span data-stu-id="59b38-216">Interceptors are powerful.</span></span> <span data-ttu-id="59b38-217">不過，在許多情況下，覆寫 SaveChanges 方法或針對 DbCoNtext 上公開的 [SaveChanges 使用 .net 事件](xref:core/logging-events-diagnostics/events) 可能比較容易。</span><span class="sxs-lookup"><span data-stu-id="59b38-217">However, in many cases it may be easier to override the SaveChanges method or use the [.NET events for SaveChanges](xref:core/logging-events-diagnostics/events) exposed on DbContext.</span></span>

### <a name="example-savechanges-interception-for-auditing"></a><span data-ttu-id="59b38-218">範例：用於審核的 SaveChanges 攔截</span><span class="sxs-lookup"><span data-stu-id="59b38-218">Example: SaveChanges interception for auditing</span></span>

<span data-ttu-id="59b38-219">您可以攔截 SaveChanges 來建立所做變更的獨立審核記錄。</span><span class="sxs-lookup"><span data-stu-id="59b38-219">SaveChanges can be intercepted to create an independent audit record of the changes made.</span></span>

> [!NOTE]
> <span data-ttu-id="59b38-220">這並不是一種強大的審核方案。</span><span class="sxs-lookup"><span data-stu-id="59b38-220">This is not intended to be a robust auditing solution.</span></span> <span data-ttu-id="59b38-221">而是用來示範攔截功能的簡單範例。</span><span class="sxs-lookup"><span data-stu-id="59b38-221">Rather it is a simplistic example used to demonstrate the features of interception.</span></span>

#### <a name="the-application-context"></a><span data-ttu-id="59b38-222">應用程式內容</span><span class="sxs-lookup"><span data-stu-id="59b38-222">The application context</span></span>

<span data-ttu-id="59b38-223">[用於進行審核的範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SaveChangesInterception)會使用包含 blog 和文章的簡單 DbCoNtext。</span><span class="sxs-lookup"><span data-stu-id="59b38-223">The [sample for auditing](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SaveChangesInterception) uses a simple DbContext with blogs and posts.</span></span>

<!--
public class BlogsContext : DbContext
{
    private readonly AuditingInterceptor _auditingInterceptor = new AuditingInterceptor("DataSource=audit.db");

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .AddInterceptors(_auditingInterceptor)
            .UseSqlite("DataSource=blogs.db");

    public DbSet<Blog> Blogs { get; set; }
}

public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    public int Id { get; set; }
    public string Title { get; set; }

    public Blog Blog { get; set; }
}
-->
[!code-csharp[BlogsContext](../../../samples/core/Miscellaneous/SaveChangesInterception/BlogsContext.cs?name=BlogsContext)]

<span data-ttu-id="59b38-224">請注意，已針對每個 DbCoNtext 實例註冊攔截器的新實例。</span><span class="sxs-lookup"><span data-stu-id="59b38-224">Notice that a new instance of the interceptor is registered for each DbContext instance.</span></span> <span data-ttu-id="59b38-225">這是因為審核攔截器包含連結至目前內容實例的狀態。</span><span class="sxs-lookup"><span data-stu-id="59b38-225">This is because the auditing interceptor contains state linked to the current context instance.</span></span>

#### <a name="the-audit-context"></a><span data-ttu-id="59b38-226">審核內容</span><span class="sxs-lookup"><span data-stu-id="59b38-226">The audit context</span></span>

<span data-ttu-id="59b38-227">此範例也包含用於審核資料庫的第二個 DbCoNtext 和模型。</span><span class="sxs-lookup"><span data-stu-id="59b38-227">The sample also contains a second DbContext and model used for the auditing database.</span></span>

<!--
public class AuditContext : DbContext
{
    private readonly string _connectionString;

    public AuditContext(string connectionString)
    {
        _connectionString = connectionString;
    }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.UseSqlite(_connectionString);

    public DbSet<SaveChangesAudit> SaveChangesAudits { get; set; }
}

public class SaveChangesAudit
{
    public int Id { get; set; }
    public Guid AuditId { get; set; }
    public DateTime StartTime { get; set; }
    public DateTime EndTime { get; set; }
    public bool Succeeded { get; set; }
    public string ErrorMessage { get; set; }

    public ICollection<EntityAudit> Entities { get; } = new List<EntityAudit>();
}

public class EntityAudit
{
    public int Id { get; set; }
    public EntityState State { get; set; }
    public string AuditMessage { get; set; }

    public SaveChangesAudit SaveChangesAudit { get; set; }
}
-->
[!code-csharp[AuditContext](../../../samples/core/Miscellaneous/SaveChangesInterception/AuditContext.cs?name=AuditContext)]

#### <a name="the-interceptor"></a><span data-ttu-id="59b38-228">攔截器</span><span class="sxs-lookup"><span data-stu-id="59b38-228">The interceptor</span></span>

<span data-ttu-id="59b38-229">使用攔截器進行審核的一般構想如下：</span><span class="sxs-lookup"><span data-stu-id="59b38-229">The general idea for auditing with the interceptor is:</span></span>

* <span data-ttu-id="59b38-230">在 SaveChanges 的開頭建立審核訊息，並將其寫入至審核資料庫</span><span class="sxs-lookup"><span data-stu-id="59b38-230">An audit message is created at the beginning of SaveChanges and is written to the auditing database</span></span>
* <span data-ttu-id="59b38-231">允許 SaveChanges 繼續</span><span class="sxs-lookup"><span data-stu-id="59b38-231">SaveChanges is allowed to continue</span></span>
* <span data-ttu-id="59b38-232">如果 SaveChanges 成功，則會更新審核訊息以指出成功</span><span class="sxs-lookup"><span data-stu-id="59b38-232">If SaveChanges succeeds, then the audit message is updated to indicate success</span></span>
* <span data-ttu-id="59b38-233">如果 SaveChanges 失敗，則會更新審核訊息以指出失敗</span><span class="sxs-lookup"><span data-stu-id="59b38-233">If SaveChanges fails, then the audit message is updated to indicate the failure</span></span>

<span data-ttu-id="59b38-234">第一個階段是在使用和的覆寫將任何變更傳送至資料庫之前處理 <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor.SavingChanges%2A?displayProperty=nameWithType> <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor.SavingChangesAsync%2A?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="59b38-234">The first stage is handled before any changes are sent to the database using overrides of <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor.SavingChanges%2A?displayProperty=nameWithType> and <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor.SavingChangesAsync%2A?displayProperty=nameWithType>.</span></span>

<!--
    public async ValueTask<InterceptionResult<int>> SavingChangesAsync(
        DbContextEventData eventData,
        InterceptionResult<int> result,
        CancellationToken cancellationToken = default)
    {
        _audit = CreateAudit(eventData.Context);

        using (var auditContext = new AuditContext(_connectionString))
        {
            auditContext.Add(_audit);
            await auditContext.SaveChangesAsync();
        }

        return result;
    }

    public InterceptionResult<int> SavingChanges(
        DbContextEventData eventData,
        InterceptionResult<int> result)
    {
        _audit = CreateAudit(eventData.Context);

        using (var auditContext = new AuditContext(_connectionString))
        {
            auditContext.Add(_audit);
            auditContext.SaveChanges();
        }

        return result;
    }
-->
[!code-csharp[SavingChanges](../../../samples/core/Miscellaneous/SaveChangesInterception/AuditingInterceptor.cs?name=SavingChanges)]

<span data-ttu-id="59b38-235">覆寫同步和非同步方法，可確保不論是否呼叫或，都會進行審核 `SaveChanges` `SaveChangesAsync` 。</span><span class="sxs-lookup"><span data-stu-id="59b38-235">Overriding both sync and async methods ensures that auditing will happen regardless of whether `SaveChanges` or `SaveChangesAsync` are called.</span></span> <span data-ttu-id="59b38-236">另外也請注意，非同步多載本身可以對審核資料庫執行非封鎖的非同步 i/o。</span><span class="sxs-lookup"><span data-stu-id="59b38-236">Notice also that the async overload is itself able to perform non-blocking async I/O to the auditing database.</span></span> <span data-ttu-id="59b38-237">您可能想要從同步方法擲回， `SavingChanges` 以確保所有資料庫 i/o 都是非同步。</span><span class="sxs-lookup"><span data-stu-id="59b38-237">You may wish to throw from the sync `SavingChanges` method to ensure that all database I/O is async.</span></span> <span data-ttu-id="59b38-238">然後，應用程式必須一律呼叫 `SaveChangesAsync` 而不是 `SaveChanges` 。</span><span class="sxs-lookup"><span data-stu-id="59b38-238">This then requires that the application always calls `SaveChangesAsync` and never `SaveChanges`.</span></span>

#### <a name="the-audit-message"></a><span data-ttu-id="59b38-239">審核訊息</span><span class="sxs-lookup"><span data-stu-id="59b38-239">The audit message</span></span>

<span data-ttu-id="59b38-240">每個攔截器方法都有一個 `eventData` 參數，以提供所攔截事件的相關內容資訊。</span><span class="sxs-lookup"><span data-stu-id="59b38-240">Every interceptor method has an `eventData` parameter providing contextual information about the event being intercepted.</span></span> <span data-ttu-id="59b38-241">在此情況下，目前的應用程式 DbCoNtext 會包含在事件資料中，然後用來建立審核訊息。</span><span class="sxs-lookup"><span data-stu-id="59b38-241">In this case the current application DbContext is included in the event data, which is then used to create an audit message.</span></span>

<!--
    private static SaveChangesAudit CreateAudit(DbContext context)
    {
        context.ChangeTracker.DetectChanges();

        var audit = new SaveChangesAudit
        {
            AuditId = Guid.NewGuid(),
            StartTime = DateTime.UtcNow
        };

        foreach (var entry in context.ChangeTracker.Entries())
        {
            var auditMessage = entry.State switch
            {
                EntityState.Deleted => CreateDeletedMessage(entry),
                EntityState.Modified => CreateModifiedMessage(entry),
                EntityState.Added => CreateAddedMessage(entry),
                _ => null
            };

            if (auditMessage != null)
            {
                audit.Entities.Add(new EntityAudit { State = entry.State, AuditMessage = auditMessage });
            }
        }

        return audit;

        string CreateAddedMessage(EntityEntry entry)
            => entry.Properties.Aggregate(
                $"Inserting {entry.Metadata.DisplayName()} with ",
                (auditString, property) => auditString + $"{property.Metadata.Name}: '{property.CurrentValue}' ");

        string CreateModifiedMessage(EntityEntry entry)
            => entry.Properties.Where(property => property.IsModified || property.Metadata.IsPrimaryKey()).Aggregate(
                $"Updating {entry.Metadata.DisplayName()} with ",
                (auditString, property) => auditString + $"{property.Metadata.Name}: '{property.CurrentValue}' ");

        string CreateDeletedMessage(EntityEntry entry)
            => entry.Properties.Where(property => property.Metadata.IsPrimaryKey()).Aggregate(
                $"Deleting {entry.Metadata.DisplayName()} with ",
                (auditString, property) => auditString + $"{property.Metadata.Name}: '{property.CurrentValue}' ");
    }
-->
[!code-csharp[CreateAudit](../../../samples/core/Miscellaneous/SaveChangesInterception/AuditingInterceptor.cs?name=CreateAudit)]

<span data-ttu-id="59b38-242">結果是 `SaveChangesAudit` 具有實體集合的實體 `EntityAudit` ，每個插入、更新或刪除都有一個實體。</span><span class="sxs-lookup"><span data-stu-id="59b38-242">The result is a `SaveChangesAudit` entity with a collection of `EntityAudit` entities, one for each insert, update, or delete.</span></span> <span data-ttu-id="59b38-243">攔截器接著會將這些實體插入 audit 資料庫中。</span><span class="sxs-lookup"><span data-stu-id="59b38-243">The interceptor then inserts these entities into the audit database.</span></span>

> [!TIP]
> <span data-ttu-id="59b38-244">ToString 會在每個 EF Core 事件資料類別中覆寫，以產生事件的對等記錄訊息。</span><span class="sxs-lookup"><span data-stu-id="59b38-244">ToString is overridden in every EF Core event data class to generate the equivalent log message for the event.</span></span> <span data-ttu-id="59b38-245">例如，呼叫會 `ContextInitializedEventData.ToString` 使用提供者 ' microsoft.entityframeworkcore. Sqlite ' （具有選項： None）來產生 "Entity Framework Core 5.0.0 初始化 ' BlogsCoNtext '。</span><span class="sxs-lookup"><span data-stu-id="59b38-245">For example, calling `ContextInitializedEventData.ToString` generates "Entity Framework Core 5.0.0 initialized 'BlogsContext' using provider 'Microsoft.EntityFrameworkCore.Sqlite' with options: None".</span></span>

#### <a name="detecting-success"></a><span data-ttu-id="59b38-246">偵測成功</span><span class="sxs-lookup"><span data-stu-id="59b38-246">Detecting success</span></span>

<span data-ttu-id="59b38-247">Audit 實體會儲存在攔截器上，如此一來，一旦 SaveChanges 成功或失敗，就可以再次存取該實體。</span><span class="sxs-lookup"><span data-stu-id="59b38-247">The audit entity is stored on the interceptor so that it can be accessed again once SaveChanges either succeeds or fails.</span></span> <span data-ttu-id="59b38-248">表示成功， <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor.SavedChanges%2A?displayProperty=nameWithType> 或 <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor.SavedChangesAsync%2A?displayProperty=nameWithType> 呼叫。</span><span class="sxs-lookup"><span data-stu-id="59b38-248">For success, <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor.SavedChanges%2A?displayProperty=nameWithType> or <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor.SavedChangesAsync%2A?displayProperty=nameWithType> is called.</span></span>

<!--
    public int SavedChanges(SaveChangesCompletedEventData eventData, int result)
    {
        using (var auditContext = new AuditContext(_connectionString))
        {
            auditContext.Attach(_audit);
            _audit.Succeeded = true;
            _audit.EndTime = DateTime.UtcNow;

            auditContext.SaveChanges();
        }

        return result;
    }

    public async ValueTask<int> SavedChangesAsync(
        SaveChangesCompletedEventData eventData,
        int result,
        CancellationToken cancellationToken = new CancellationToken())
    {
        using (var auditContext = new AuditContext(_connectionString))
        {
            auditContext.Attach(_audit);
            _audit.Succeeded = true;
            _audit.EndTime = DateTime.UtcNow;

            await auditContext.SaveChangesAsync(cancellationToken);
        }

        return result;
    }
-->
[!code-csharp[SavedChanges](../../../samples/core/Miscellaneous/SaveChangesInterception/AuditingInterceptor.cs?name=SavedChanges)]

<span data-ttu-id="59b38-249">Audit 實體會附加至 audit 內容，因為它已經存在於資料庫中，因此需要更新。</span><span class="sxs-lookup"><span data-stu-id="59b38-249">The audit entity is attached to the audit context, since it already exists in the database and needs to be updated.</span></span> <span data-ttu-id="59b38-250">接著，我們 `Succeeded` 會設定，並將 `EndTime` 這些屬性標示為已修改，讓 SaveChanges 將更新傳送至 audit 資料庫。</span><span class="sxs-lookup"><span data-stu-id="59b38-250">We then set `Succeeded` and `EndTime`, which marks these properties as modified so SaveChanges will send an update to the audit database.</span></span>

#### <a name="detecting-failure"></a><span data-ttu-id="59b38-251">偵測失敗</span><span class="sxs-lookup"><span data-stu-id="59b38-251">Detecting failure</span></span>

<span data-ttu-id="59b38-252">失敗的處理方式與成功相同，但在 <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor.SaveChangesFailed%2A?displayProperty=nameWithType> 或 <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor.SaveChangesFailedAsync%2A?displayProperty=nameWithType> 方法中。</span><span class="sxs-lookup"><span data-stu-id="59b38-252">Failure is handled in much the same way as success, but in the <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor.SaveChangesFailed%2A?displayProperty=nameWithType> or <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor.SaveChangesFailedAsync%2A?displayProperty=nameWithType> method.</span></span> <span data-ttu-id="59b38-253">事件資料包含擲回的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="59b38-253">The event data contains the exception that was thrown.</span></span>

<!--
    public void SaveChangesFailed(DbContextErrorEventData eventData)
    {
        using (var auditContext = new AuditContext(_connectionString))
        {
            auditContext.Attach(_audit);
            _audit.Succeeded = false;
            _audit.EndTime = DateTime.UtcNow;
            _audit.ErrorMessage = eventData.Exception.Message;

            auditContext.SaveChanges();
        }
    }

    public async Task SaveChangesFailedAsync(
        DbContextErrorEventData eventData,
        CancellationToken cancellationToken = new CancellationToken())
    {
        using (var auditContext = new AuditContext(_connectionString))
        {
            auditContext.Attach(_audit);
            _audit.Succeeded = false;
            _audit.EndTime = DateTime.UtcNow;
            _audit.ErrorMessage = eventData.Exception.InnerException?.Message;

            await auditContext.SaveChangesAsync(cancellationToken);
        }
    }
-->
[!code-csharp[SaveChangesFailed](../../../samples/core/Miscellaneous/SaveChangesInterception/AuditingInterceptor.cs?name=SaveChangesFailed)]

#### <a name="demonstration"></a><span data-ttu-id="59b38-254">示範</span><span class="sxs-lookup"><span data-stu-id="59b38-254">Demonstration</span></span>

<span data-ttu-id="59b38-255">此 [審核範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SaveChangesInterception) 包含一個簡單的主控台應用程式，可對 [日誌] 資料庫進行變更，然後顯示所建立的審核。</span><span class="sxs-lookup"><span data-stu-id="59b38-255">The [auditing sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SaveChangesInterception) contains a simple console application that makes changes to the blogging database and then shows the auditing that was created.</span></span>

<!--
        // Insert, update, and delete some entities

        using (var context = new BlogsContext())
        {
            context.Add(
                new Blog
                {
                    Name = "EF Blog",
                    Posts =
                    {
                        new Post { Title = "EF Core 3.1!" },
                        new Post { Title = "EF Core 5.0!" }
                    }
                });

            await context.SaveChangesAsync();
        }

        using (var context = new BlogsContext())
        {
            var blog = context.Blogs.Include(e => e.Posts).Single();

            blog.Name = "EF Core Blog";
            context.Remove(blog.Posts.First());
            blog.Posts.Add(new Post { Title = "EF Core 6.0!" });

            context.SaveChanges();
        }

        // Do an insert that will fail

        using (var context = new BlogsContext())
        {
            try
            {
                context.Add(new Post { Id = 3, Title = "EF Core 3.1!" });

                await context.SaveChangesAsync();
            }
            catch (DbUpdateException)
            {
            }
        }

        // Look at the audit trail

        using (var context = new AuditContext("DataSource=audit.db"))
        {
            foreach (var audit in context.SaveChangesAudits.Include(e => e.Entities).ToList())
            {
                Console.WriteLine(
                    $"Audit {audit.AuditId} from {audit.StartTime} to {audit.EndTime} was{(audit.Succeeded ? "" : " not")} successful.");

                foreach (var entity in audit.Entities)
                {
                    Console.WriteLine($"  {entity.AuditMessage}");
                }

                if (!audit.Succeeded)
                {
                    Console.WriteLine($"  Error: {audit.ErrorMessage}");
                }
            }
        }
-->
[!code-csharp[Program](../../../samples/core/Miscellaneous/SaveChangesInterception/Program.cs?name=Program)]

<span data-ttu-id="59b38-256">結果會顯示 [審核] 資料庫的內容：</span><span class="sxs-lookup"><span data-stu-id="59b38-256">The result shows the contents of the auditing database:</span></span>

```output
Audit 52e94327-1767-4046-a3ca-4c6b1eecbca6 from 10/14/2020 9:10:17 PM to 10/14/2020 9:10:17 PM was successful.
  Inserting Blog with Id: '-2147482647' Name: 'EF Blog'
  Inserting Post with Id: '-2147482647' BlogId: '-2147482647' Title: 'EF Core 3.1!'
  Inserting Post with Id: '-2147482646' BlogId: '-2147482647' Title: 'EF Core 5.0!'
Audit 8450f57a-5030-4211-a534-eb66b8da7040 from 10/14/2020 9:10:17 PM to 10/14/2020 9:10:17 PM was successful.
  Inserting Post with Id: '-2147482645' BlogId: '1' Title: 'EF Core 6.0!'
  Updating Blog with Id: '1' Name: 'EF Core Blog'
  Deleting Post with Id: '1'
Audit 201fef4d-66a7-43ad-b9b6-b57e9d3f37b3 from 10/14/2020 9:10:17 PM to 10/14/2020 9:10:17 PM was not successful.
  Inserting Post with Id: '3' BlogId: '' Title: 'EF Core 3.1!'
  Error: SQLite Error 19: 'UNIQUE constraint failed: Post.Id'.
```
