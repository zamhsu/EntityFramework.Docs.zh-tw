---
title: 攔截器-EF Core
description: 攔截資料庫作業和其他事件
author: ajcvickers
ms.date: 10/08/2020
uid: core/logging-events-diagnostics/interceptors
ms.openlocfilehash: 22d860a083c5ece9be109be630c3ce01dd742bf2
ms.sourcegitcommit: 788a56c2248523967b846bcca0e98c2ed7ef0d6b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "95003403"
---
# <a name="interceptors"></a>攔截器

Entity Framework Core (EF Core) 攔截器可以攔截、修改及/或隱藏 EF Core 作業。 這包括執行命令等低層級的資料庫作業，以及較高層級的作業，例如對 SaveChanges 的呼叫。

攔截器與記錄和診斷不同，因為它們允許修改或隱藏正在攔截的作業。 [簡易記錄](xref:core/logging-events-diagnostics/simple-logging) 或 [Microsoft Extensions。記錄](xref:core/logging-events-diagnostics/extensions-logging) 是較佳的記錄選擇。

當設定內容時，會針對每個 DbCoNtext 實例註冊攔截器。 您可以使用 [診斷](xref:core/logging-events-diagnostics/diagnostic-listeners) 接聽程式來取得相同資訊，但會針對進程中的所有 DbCoNtext 實例取得相同的資訊。

## <a name="registering-interceptors"></a>註冊攔截器

設定 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.AddInterceptors%2A> [DbCoNtext 實例](xref:core/dbcontext-configuration/index)時，會使用註冊攔截器。 這通常是在的覆寫中完成 <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType> 。 例如：

<!--
public class ExampleContext : BlogsContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder) 
        => optionsBuilder.AddInterceptors(new TaggedQueryCommandInterceptor());
}
-->
[!code-csharp[RegisterInterceptor](../../../samples/core/Miscellaneous/CommandInterception/Program.cs?name=RegisterInterceptor)]

或者，您也 `AddInterceptors` 可以在 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> 建立 <xref:Microsoft.EntityFrameworkCore.DbContextOptions> 實例以傳遞至 DbCoNtext 的函式時，將其當作的一部分來呼叫。

> [!TIP]
> 當使用 AddDbCoNtext 或將 DbCoNtextOptions 實例傳遞至 DbCoNtext 的函式時，仍會呼叫 OnConfiguring。 這讓它成為套用內容設定的理想位置，不論 DbCoNtext 的結構為何。

攔截器通常是無狀態的，這表示單一攔截器實例可用於所有 DbCoNtext 實例。 例如：

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

每個攔截器實例都必須執行一或多個衍生自的介面 <xref:Microsoft.EntityFrameworkCore.Diagnostics.IInterceptor> 。 每個實例都應該只註冊一次，即使它會執行多個攔截介面;EF Core 會適當地路由每個介面的事件。

## <a name="database-interception"></a>資料庫攔截

> [!NOTE]
> 資料庫攔截是在 EF Core 3.0 中引進，而且僅適用于關係資料庫提供者。
> EF Core 5.0 中引進了儲存點支援。

低層級資料庫攔截會分割成下表所示的三個介面。

| 攔截 器                                                            | 資料庫作業遭到攔截
|:-----------------------------------------------------------------------|-------------------------------------------------
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbCommandInterceptor> | 建立命令</br>執行命令</br>命令失敗</br>處置命令的 DbDataReader
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbConnectionInterceptor> | 開啟和關閉連接</br>連接失敗
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbTransactionInterceptor> | 建立交易</br>使用現有的交易</br>認可交易</br>回復交易</br>建立和使用儲存點</br>交易失敗

基類 <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor> 、 <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbConnectionInterceptor> 和 <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbTransactionInterceptor> 包含對應介面中每個方法的無作業執行。 使用基類，以避免需要執行未使用的攔截方法。

每個攔截器類型上的方法都是成對的，第一次是在資料庫作業啟動之前呼叫，第二個則是在作業完成之後呼叫。 例如， 例如，在 <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor.ReaderExecuting%2A?displayProperty=nameWithType> 執行查詢之前呼叫，並 <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor.ReaderExecuted%2A?displayProperty=nameWithType> 在查詢傳送至資料庫之後呼叫。

每一對方法都有同步和非同步變化。 這可讓非同步 i/o （例如要求存取權杖）在攔截非同步資料庫作業時發生。

### <a name="example-command-interception-to-add-query-hints"></a>範例：命令攔截以新增查詢提示

> [!TIP]  
> 您可以從 GitHub [下載命令攔截器範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/CommandInterception) 。

<xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbCommandInterceptor>可以用來修改 SQL，然後再傳送至資料庫。 此範例顯示如何修改 SQL 以包含查詢提示。

攔截的最棘手部分通常會判斷命令是否對應到需要修改的查詢。 剖析 SQL 是一個選項，但通常是脆弱的。 另一個選項是使用 [EF Core 的查詢標記](xref:core/querying/tags) 來標記每個應該修改的查詢。 例如：

<!--
            var blogs1 = context.Blogs.TagWith("Use hint: robust plan").ToList();
-->
[!code-csharp[TaggedQuery](../../../samples/core/Miscellaneous/CommandInterception/Program.cs?name=TaggedQuery)]

然後可以在攔截器中偵測到這個標記，因為它一律會以批註的形式包含在命令文字的第一行中。 偵測到標記時，會修改查詢 SQL 以加入適當的提示：

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

注意：

* 攔截器會繼承自 <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor> ，以避免必須在攔截器介面中執行每個方法。
* 攔截器會同時實行同步處理和非同步方法。 這可確保相同的查詢提示會套用到同步和非同步查詢。
* 攔截器會在 `Executing` 傳送至資料庫 _之前，先_ 在產生的 SQL 中，執行 EF Core 所呼叫的方法。 將這與在 `Executed` 資料庫呼叫傳回之後呼叫的方法做對比。

執行此範例中的程式碼時，會在標記查詢時產生下列程式碼：

```sql
-- Use hint: robust plan

SELECT [b].[Id], [b].[Name]
FROM [Blogs] AS [b] OPTION (ROBUST PLAN)
```

另一方面，當查詢未加上標籤時，就會將它傳送至未修改的資料庫：

```sql
SELECT [b].[Id], [b].[Name]
FROM [Blogs] AS [b]
```

### <a name="example-connection-interception-for-sql-azure-authentication-using-add"></a>範例：使用 ADD 的 SQL Azure 驗證連接攔截

> [!TIP]  
> 您可以從 GitHub [下載連接攔截器範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/ConnectionInterception) 。

<xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbConnectionInterceptor>可以用來在 <xref:System.Data.Common.DbConnection> 用來連接到資料庫之前，先操作。 這可以用來取得 Azure Active Directory (AAD) 存取權杖。 例如：

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
> [SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient/) 現在透過連接字串支援 AAD 驗證。 如需詳細資訊，請參閱 <xref:Microsoft.Data.SqlClient.SqlAuthenticationMethod>。

> [!WARNING]
> 請注意，如果進行同步呼叫以開啟連接，攔截器就會擲回。 這是因為沒有任何非非同步方法可以取得存取權杖，而且沒有 [任何通用且簡單的方法可從非非同步內容呼叫非同步方法，而不會產生風險鎖死](https://devblogs.microsoft.com/dotnet/configureawait-faq/)。

> [!WARNING]
> 在某些情況下，可能不會自動快取 Azure 權杖提供者的存取權杖。 視所要求的權杖類型而定，您可能需要在此執行自己的快取。

### <a name="example-advanced-command-interception-for-caching"></a>範例：快取的 Advanced 命令攔截

> [!TIP]  
> 您可以從 GitHub [下載 advanced command 攔截器範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/CachingInterception) 。

EF Core 攔截器可以：

* 告訴 EF Core 隱藏執行正在攔截的作業
* 將回報的作業結果變更回 EF Core

此範例顯示使用這些功能的攔截器，其行為就像基本的第二層快取。 針對特定的查詢會傳回快取的查詢結果，以避免資料庫往返。

> [!WARNING]
> 以這種方式變更 EF Core 預設行為時，請小心。 如果發生無法正確處理的異常結果，EF Core 可能會以非預期的方式表現。 此外，此範例會示範攔截器概念;它不適合作為健全的第二層快取執行的範本。

在此範例中，應用程式會經常執行查詢來取得最新的「每日訊息」：

<!--
        async Task<string> GetDailyMessage(DailyMessageContext context)
            => (await context.DailyMessages.TagWith("Get_Daily_Message").OrderBy(e => e.Id).LastAsync()).Message;
-->
[!code-csharp[GetDailyMessage](../../../samples/core/Miscellaneous/CachingInterception/Program.cs?name=GetDailyMessage)]

此查詢已 [標記](xref:core/querying/tags) 為可在攔截器中輕鬆地偵測到。 其構想是每天只查詢一次新訊息的資料庫。 有時候，應用程式會使用快取的結果。  (範例會在範例中使用10秒的延遲，以模擬新的一天。 ) 

#### <a name="interceptor-state"></a>攔截器狀態

此攔截器是具狀態的：它會儲存最近查詢過的每日訊息的識別碼和郵件內文，以及執行該查詢的時間。 因為此狀態，所以我們也需要 [鎖定](/dotnet/csharp/language-reference/keywords/lock-statement) ，因為快取需要相同的攔截器必須由多個內容實例使用。

<!--
    private readonly object _lock = new object();
    private int _id;
    private string _message;
    private DateTime _queriedAt;
-->
[!code-csharp[InterceptorState](../../../samples/core/Miscellaneous/CachingInterception/CachingCommandInterceptor.cs?name=InterceptorState)]

#### <a name="before-execution"></a>執行前

在 `Executing` 方法 (亦即進行資料庫呼叫之前) ，攔截器會偵測已標記的查詢，然後檢查是否有快取的結果。 如果找到這樣的結果，則會抑制查詢並改用快取的結果。

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

請注意程式碼如何呼叫 <xref:Microsoft.EntityFrameworkCore.Diagnostics.InterceptionResult%601.SuppressWithResult%2A?displayProperty=nameWithType> 和傳遞包含快取資料的取代 <xref:System.Data.Common.DbDataReader> 。 然後會傳回這個 InterceptionResult，而導致查詢執行隱藏。 EF Core 會使用取代讀取器作為查詢的結果。

此攔截器也會操控命令文字。 這並非必要的操作，但可改善記錄訊息中的清楚明瞭。 因為目前不會執行查詢，所以命令文字不需要是有效的 SQL。

#### <a name="after-execution"></a>執行之後

如果沒有可用的快取訊息，或已過期，則上述程式碼不會隱藏結果。 因此 EF Core 會正常執行查詢。 然後，它會在執行之後返回攔截器的 `Executed` 方法。 此時，如果結果不是快取的讀取器，則會從實際讀取器 exacted 新的訊息識別碼和字串，並在下次使用此查詢時加以快取。

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

#### <a name="demonstration"></a>示範

快取 [攔截器範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/CachingInterception) 包含一個簡單的主控台應用程式，可查詢每日訊息來測試快取：

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

這會導致下列輸出：

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

請注意，在記錄檔輸出中，應用程式會繼續使用快取的訊息，直到超時過期為止，此時會重新查詢資料庫中是否有任何新訊息。

## <a name="savechanges-interception"></a>SaveChanges 攔截

> [!NOTE]
> SaveChanges 攔截是在 EF Core 5.0 中引進。

> [!TIP]  
> 您可以從 GitHub [下載 SaveChanges 攔截器範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SaveChangesInterception) 。

<xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> 和 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A> 攔截點是由 `ISaveChangesInterceptor` <!-- Issue #2748 --> 介面。 針對其他攔截器， `SaveChangesInterceptor` <!-- Issue #2748 --> 提供沒有 op 方法的基類是為了方便起見。

> [!TIP]
> 攔截器功能強大。 不過，在許多情況下，覆寫 SaveChanges 方法或針對 DbCoNtext 上公開的 [SaveChanges 使用 .net 事件](xref:core/logging-events-diagnostics/events) 可能比較容易。

### <a name="example-savechanges-interception-for-auditing"></a>範例：用於審核的 SaveChanges 攔截

您可以攔截 SaveChanges 來建立所做變更的獨立審核記錄。

> [!NOTE]
> 這並不是一種強大的審核方案。 而是用來示範攔截功能的簡單範例。

#### <a name="the-application-context"></a>應用程式內容

[用於進行審核的範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SaveChangesInterception)會使用包含 blog 和文章的簡單 DbCoNtext。

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

請注意，已針對每個 DbCoNtext 實例註冊攔截器的新實例。 這是因為審核攔截器包含連結至目前內容實例的狀態。

#### <a name="the-audit-context"></a>審核內容

此範例也包含用於審核資料庫的第二個 DbCoNtext 和模型。

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

#### <a name="the-interceptor"></a>攔截器

使用攔截器進行審核的一般構想如下：

* 在 SaveChanges 的開頭建立審核訊息，並將其寫入至審核資料庫
* 允許 SaveChanges 繼續
* 如果 SaveChanges 成功，則會更新審核訊息以指出成功
* 如果 SaveChanges 失敗，則會更新審核訊息以指出失敗

第一個階段會在任何變更傳送至資料庫之前先處理，並使用的覆寫 `ISaveChangesInterceptor.SavingChanges` <!-- Issue #2748 --> 和 `ISaveChangesInterceptor.SavingChangesAsync`<!-- Issue #2748 -->.

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

覆寫同步處理和非同步方法，可確保不論是否呼叫 SaveChanges 或 SaveChangesAsync，都會發生審核。 另外也請注意，非同步多載本身可以對審核資料庫執行非封鎖的非同步 i/o。 您可能想要從同步處理 SavingChanges 方法擲回，以確保所有資料庫 i/o 都是非同步。 然後，應用程式必須一律呼叫 SaveChangesAsync，而不是 SaveChanges。

#### <a name="the-audit-message"></a>審核訊息

每個攔截器方法都有一個 `eventData` 參數，以提供所攔截事件的相關內容資訊。 在此情況下，目前的應用程式 DbCoNtext 會包含在事件資料中，然後用來建立審核訊息。

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

結果是 `SaveChangesAudit` 具有實體集合的實體 `EntityAudit` ，每個插入、更新或刪除都有一個實體。 攔截器接著會將這些實體插入 audit 資料庫中。

> [!TIP]
> ToString 會在每個 EF Core 事件資料類別中覆寫，以產生事件的對等記錄訊息。 例如，呼叫會 `ContextInitializedEventData.ToString` 使用提供者 ' microsoft.entityframeworkcore. Sqlite ' （具有選項： None）來產生 "Entity Framework Core 5.0.0 初始化 ' BlogsCoNtext '。

#### <a name="detecting-success"></a>偵測成功

Audit 實體會儲存在攔截器上，如此一來，一旦 SaveChanges 成功或失敗，就可以再次存取該實體。 若為成功， `ISaveChangesInterceptor.SavedChanges` <!-- Issue #2748 --> 或 `ISaveChangesInterceptor.SavedChangesAsync` <!-- Issue #2748 --> 呼叫 。

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

Audit 實體會附加至 audit 內容，因為它已經存在於資料庫中，因此需要更新。 接著，我們 `Succeeded` 會設定，並將 `EndTime` 這些屬性標示為已修改，讓 SaveChanges 將更新傳送至 audit 資料庫。

#### <a name="detecting-failure"></a>偵測失敗

失敗的處理方式非常類似于成功，但在 `ISaveChangesInterceptor.SaveChangesFailed` <!-- Issue #2748 --> 或 `ISaveChangesInterceptor.SaveChangesFailedAsync` <!-- Issue #2748 --> 方法。 事件資料包含擲回的例外狀況。

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

#### <a name="demonstration"></a>示範

此 [審核範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SaveChangesInterception) 包含一個簡單的主控台應用程式，可對 [日誌] 資料庫進行變更，然後顯示所建立的審核。

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

結果會顯示 [審核] 資料庫的內容：

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
