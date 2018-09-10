---
title: 記錄和攔截資料庫作業-EF6
author: divega
ms.date: 2016-10-23
ms.assetid: b5ee7eb1-88cc-456e-b53c-c67e24c3f8ca
ms.openlocfilehash: 9a8be81af45d9f27caa8c26f66d219dc568b6604
ms.sourcegitcommit: 0d36e8ff0892b7f034b765b15e041f375f88579a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2018
ms.locfileid: "44251267"
---
# <a name="logging-and-intercepting-database-operations"></a>記錄和攔截資料庫作業
> [!NOTE]
> **僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。 如果您使用的是較早版本，則不適用部分或全部的資訊。  

應用程式程式碼時，可以被攔截從 Entity Framework 6，每當 Entity Framework 會將命令傳送至資料庫這個命令。 這最常用於記錄 SQL，但也可用來修改，或者中止命令。  

具體來說，EF 包含：  

- 記錄檔的內容，類似於 LINQ to SQL 中的 DataContext.Log 內容  
- 一種機制，以自訂的內容和格式化的輸出傳送至記錄檔  
- 提供更高的控制彈性的攔截低階建置組塊  

## <a name="context-log-property"></a>內容記錄屬性  

DbContext.Database.Log 屬性可以設為可接受字串的任何方法的委派。 通常它搭配任何 TextWriter 藉由將它設定為該 TextWriter 的 「 寫入 」 方法。 目前的內容所產生的所有 SQL 將會都記錄該寫入器。 例如，下列程式碼會記錄至主控台的 SQL:  

``` csharp
using (var context = new BlogContext())
{
    context.Database.Log = Console.Write;

    // Your code here...
}
```  

請注意該內容。Console.Write 設 Database.Log。 這是所有所需的 SQL 記錄至主控台。  

讓我們加入一些簡單的查詢/插入/更新程式碼，以便我們可以看到一些輸出：  

``` csharp
using (var context = new BlogContext())
{
    context.Database.Log = Console.Write;

    var blog = context.Blogs.First(b => b.Title == "One Unicorn");

    blog.Posts.First().Title = "Green Eggs and Ham";

    blog.Posts.Add(new Post { Title = "I do not like them!" });

    context.SaveChangesAsync().Wait();
}
```  

這會產生下列輸出：  

``` SQL
SELECT TOP (1)
    [Extent1].[Id] AS [Id],
    [Extent1].[Title] AS [Title]
    FROM [dbo].[Blogs] AS [Extent1]
    WHERE (N'One Unicorn' = [Extent1].[Title]) AND ([Extent1].[Title] IS NOT NULL)
-- Executing at 10/8/2013 10:55:41 AM -07:00
-- Completed in 4 ms with result: SqlDataReader

SELECT
    [Extent1].[Id] AS [Id],
    [Extent1].[Title] AS [Title],
    [Extent1].[BlogId] AS [BlogId]
    FROM [dbo].[Posts] AS [Extent1]
    WHERE [Extent1].[BlogId] = @EntityKeyValue1
-- EntityKeyValue1: '1' (Type = Int32)
-- Executing at 10/8/2013 10:55:41 AM -07:00
-- Completed in 2 ms with result: SqlDataReader

UPDATE [dbo].[Posts]
SET [Title] = @0
WHERE ([Id] = @1)
-- @0: 'Green Eggs and Ham' (Type = String, Size = -1)
-- @1: '1' (Type = Int32)
-- Executing asynchronously at 10/8/2013 10:55:41 AM -07:00
-- Completed in 12 ms with result: 1

INSERT [dbo].[Posts]([Title], [BlogId])
VALUES (@0, @1)
SELECT [Id]
FROM [dbo].[Posts]
WHERE @@ROWCOUNT > 0 AND [Id] = scope_identity()
-- @0: 'I do not like them!' (Type = String, Size = -1)
-- @1: '1' (Type = Int32)
-- Executing asynchronously at 10/8/2013 10:55:41 AM -07:00
-- Completed in 2 ms with result: SqlDataReader
```  

（請注意，這是假設已發生任何資料庫初始設定的輸出。 如果資料庫初始化時也不已經發生則會有更多的輸出顯示所有工作的移轉會在幕後，檢查或建立新的資料庫。）  

## <a name="what-gets-logged"></a>取得記錄的內容？  

將記錄記錄屬性時設定下列各項：  

- SQL 命令的所有不同的類型。 例如:   
    - 查詢，包括一般 LINQ 查詢、 eSQL 查詢，以及原始的查詢，例如 SqlQuery 方法  
    - 插入、 更新和刪除產生的 SaveChanges  
    - 正在載入查詢例如消極式載入所產生的關聯性  
- 參數  
- 是否以非同步方式執行命令  
- 時間戳記，指出啟動執行的命令  
- 已順利完成，此命令無法藉由擲回例外狀況，或如需非同步，已取消  
- 結果值的某些指示  
- 執行命令所花費的大約時間量。 請注意，這將命令傳送到取回結果物件的時間。 它不包含讀取結果的時間。  

看看上面的範例輸出，每個記錄的四個命令是：  

- 查詢內容呼叫所產生。Blogs.First  
    - 請注意，因為此查詢會無法運作的 ToString 方法取得 SQL"First"不會提供無法在其呼叫 ToString 的 IQueryable  
- 查詢所產生的消極式載入的部落格。貼文  
    - 請注意，在發生的消極式載入的金鑰值的參數詳細資料  
    - 只有設定為非預設值的屬性參數的記錄。 例如，[大小] 屬性只會顯示它是否不是零。  
- 兩個命令所產生的 SaveChangesAsync;另一個用於更新，以變更文章標題，另一個則用於在 [插入] 來新增新的貼文  
    - 請注意的 FK 和 Title 屬性的參數詳細資料  
    - 請注意這些命令會以非同步方式執行  

## <a name="logging-to-different-places"></a>記錄到不同的地方  

記錄至如上所示的主控台是超級簡單。 它也很容易使用各種記錄記憶體、 檔案等等的[TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx)。  

如果您已熟悉 SQL，您可能會發現，在 LINQ to SQL 記錄檔的內容設定為實際 TextWriter 物件 (例如 Console.Out) 時記錄設定為接受字串 （例如方法的 EF 中的 LINQConsole.Write 或 Console.Out.Write)。 這是讓 EF 從 TextWriter 分離接受任何委派，可做為接收器的字串。 例如，假設您已經有一些記錄架構，它會定義記錄方法就像這樣：  

``` csharp
public class MyLogger
{
    public void Log(string component, string message)
    {
        Console.WriteLine("Component: {0} Message: {1} ", component, message);
    }
}
```  

這無法連接到 EF 記錄檔的內容如下：  

``` csharp
var logger = new MyLogger();
context.Database.Log = s => logger.Log("EFApp", s);
```  

## <a name="result-logging"></a>結果記錄  

預設記錄器記錄命令文字 (SQL)、 參數和 「 執行 」 列加上時間戳記之前的命令傳送至資料庫。 「 已完成 」 包含已耗用時間的資料行是記錄的執行下列命令。  

請注意，如需非同步命令的 「 已完成 」 的一行時，不會記錄直到非同步工作實際完成、 失敗，或已取消。  

「 已完成 」 列包含不同的資訊，根據命令和已成功執行的類型。  

### <a name="successful-execution"></a>成功執行  

若是成功完成輸出的命令是 「 x ms 結果中的已完成: 」 後面的結果為何的某些指示。 傳回結果的資料讀取器的命令的指示是種[DbDataReader](https://msdn.microsoft.com/library/system.data.common.dbdatareader.aspx)傳回。 傳回整數值，例如更新的命令如上所示的結果所示的命令會為整數。  

### <a name="failed-execution"></a>執行失敗  

如需藉由擲回例外狀況失敗的命令，輸出會包含例外狀況的訊息。 比方說，使用 SqlQuery 來查詢存在的資料表會記錄檔中的結果輸出類似如下：  

``` SQL
SELECT * from ThisTableIsMissing
-- Executing at 5/13/2013 10:19:05 AM
-- Failed in 1 ms with error: Invalid object name 'ThisTableIsMissing'.
```  

### <a name="canceled-execution"></a>已取消的執行  

非同步命令取消工作時，結果可能會失敗並發生例外狀況，因為這是什麼基礎 ADO.NET 提供者通常會嘗試取消時。 如果這不會發生，且工作已完全取消，則輸出會看起來像這樣：  

```  
update Blogs set Title = 'No' where Id = -1
-- Executing asynchronously at 5/13/2013 10:21:10 AM
-- Canceled in 1 ms
```  

## <a name="changing-log-content-and-formatting"></a>變更記錄檔的內容和格式  

在幕後屬性會使 Database.Log DatabaseLogFormatter 物件使用。 這個物件有效地繫結的委派，接受字串和 DbContext IDbCommandInterceptor 實作 （如下所示）。 這表示，在 DatabaseLogFormatter 會呼叫方法之前和之後執行命令的 EF。 這些 DatabaseLogFormatter 方法收集和記錄檔輸出格式化並將它傳送給委派。  

### <a name="customizing-databaselogformatter"></a>自訂 DatabaseLogFormatter  

變更記錄的內容和格式的方式，即可建立新的類別衍生自 DatabaseLogFormatter 並覆寫適當的方法。 若要覆寫最常見的方法如下：  

- LogCommand-覆寫此變更會在執行之前的記錄命令的方式。 根據預設 LogCommand 呼叫 LogParameter 針對每個參數;您可以選擇覆寫中相同，或改為以不同方式處理參數。  
- LogResult-覆寫這個值來變更執行命令的結果記錄的方式。  
- LogParameter-覆寫此選項以變更與參數記錄的內容。  

例如，假設我們想要記錄只有一行，每個命令傳送至資料庫之前。 這可以透過兩個覆寫來完成：  

- 覆寫要格式化和寫入 SQL 單行 LogCommand  
- 覆寫 LogResult 不執行任何動作。  

程式碼看起來像這樣：

``` csharp
public class OneLineFormatter : DatabaseLogFormatter
{
    public OneLineFormatter(DbContext context, Action<string> writeAction)
        : base(context, writeAction)
    {
    }

    public override void LogCommand<TResult>(
        DbCommand command, DbCommandInterceptionContext<TResult> interceptionContext)
    {
        Write(string.Format(
            "Context '{0}' is executing command '{1}'{2}",
            Context.GetType().Name,
            command.CommandText.Replace(Environment.NewLine, ""),
            Environment.NewLine));
    }

    public override void LogResult<TResult>(
        DbCommand command, DbCommandInterceptionContext<TResult> interceptionContext)
    {
    }
}
```  

若要記錄輸出只呼叫 Write 方法會將輸出傳送給已設定的寫入委派。  

（請注意，此程式碼會簡化移除分行符號只是做為範例。 它可能不適用於檢視複雜的 SQL。）  

### <a name="setting-the-databaselogformatter"></a>設定 DatabaseLogFormatter  

新的 DatabaseLogFormatter 類別建立之後就必須使用 EF 註冊。 這是使用程式碼為基礎的組態。 簡單的說這表示建立新的類別衍生自 DbConfiguration 中，在您的 DbContext 類別相同的組件，並在這個新類別的建構函式呼叫 SetDatabaseLogFormatter。 例如:   

``` csharp
public class MyDbConfiguration : DbConfiguration
{
    public MyDbConfiguration()
    {
        SetDatabaseLogFormatter(
            (context, writeAction) => new OneLineFormatter(context, writeAction));
    }
}
```  

### <a name="using-the-new-databaselogformatter"></a>使用新的 DatabaseLogFormatter  

每當設定 Database.Log，現在將會使用這個新 DatabaseLogFormatter。 因此，第 1 部分中執行程式碼會立即導致下列輸出：  

```  
Context 'BlogContext' is executing command 'SELECT TOP (1) [Extent1].[Id] AS [Id], [Extent1].[Title] AS [Title]FROM [dbo].[Blogs] AS [Extent1]WHERE (N'One Unicorn' = [Extent1].[Title]) AND ([Extent1].[Title] IS NOT NULL)'
Context 'BlogContext' is executing command 'SELECT [Extent1].[Id] AS [Id], [Extent1].[Title] AS [Title], [Extent1].[BlogId] AS [BlogId]FROM [dbo].[Posts] AS [Extent1]WHERE [Extent1].[BlogId] = @EntityKeyValue1'
Context 'BlogContext' is executing command 'update [dbo].[Posts]set [Title] = @0where ([Id] = @1)'
Context 'BlogContext' is executing command 'insert [dbo].[Posts]([Title], [BlogId])values (@0, @1)select [Id]from [dbo].[Posts]where @@rowcount > 0 and [Id] = scope_identity()'
```  

## <a name="interception-building-blocks"></a>攔截建置組塊  

到目前為止我們已探討如何使用 DbContext.Database.Log 記錄 EF 所產生的 SQL。 但這段程式碼透過更一般的攔截的一些低階建置組塊是實際的相當精簡的外觀。  

### <a name="interception-interfaces"></a>攔截介面  

攔截程式碼會攔截介面的概念而建置。 這些介面繼承自 IDbInterceptor，並定義當 EF 執行某個動作時所呼叫的方法。 目的是物件的要有一個介面，每個類型被攔截。 比方說，IDbCommandInterceptor 介面定義之前 EF 會呼叫 ExecuteNonQuery、 ExecuteScalar、 ExecuteReader 和相關的方法，呼叫的方法。 同樣地，介面會定義每個作業完成時呼叫的方法。 我們探討了上述 DatabaseLogFormatter 類別會實作這個介面來記錄的命令。  

### <a name="the-interception-context"></a>攔截內容  

它查看任何攔截器介面上定義的方法是很明顯，每次呼叫指定的型別的物件 DbInterceptionContext 或某種類型衍生自這例如 DbCommandInterceptionContext\<\>。 此物件包含內容資訊所花的 EF 的動作。 例如，如果此動作會 DbContext 代表正在執行，DbContext 包含在 DbInterceptionContext。 同樣地，對於以非同步方式執行的命令，IsAsync 旗標上設定 DbCommandInterceptionContext。  

### <a name="result-handling"></a>結果處理  

DbCommandInterceptionContext\< \>類別包含名為結果、 OriginalResult、 例外狀況，以及 OriginalException 的屬性。 這些屬性設定為 null/零對執行作業之前稱為攔截方法的呼叫 — 也就是為...執行方法。 如果作業執行，且成功，然後結果和 OriginalResult 會設定為作業的結果。 這些值接著會出現在執行此作業之後稱為攔截方法 — 也就是上...執行的方法。 同樣地，如果作業會擲回，然後 例外 和 OriginalException 屬性會設定。  

#### <a name="suppressing-execution"></a>隱藏執行  

如果攔截器設定的 Result 屬性之前已執行的命令 (其中一種...執行方法） 然後 EF 實際執行的命令，將不會嘗試，但會改為只使用結果集。 換句話說，攔截器可以隱藏執行命令，但有 EF 繼續執行命令。  

這可能用法的範例是命令批次，過去已完成，但包裝提供者。 攔截器會儲存供稍後執行命令，以批次，但會 「 假裝 」 ef 命令有正常執行。 請注意，它需要比這更實作批次處理，但這是如何變更攔截結果可能會使用的範例。  

也可以隱藏執行中的其中一個設定的例外狀況屬性...執行方法。 這會導致 EF 繼續執行作業失敗所擲回指定的例外狀況。 這當然，可能會導致應用程式當機，但也可能是暫時性例外狀況或其他一些 EF 所處理的例外狀況。 比方說，這可用在測試環境中測試應用程式的行為，命令執行失敗時。  

#### <a name="changing-the-result-after-execution"></a>在執行後變更結果  

如果攔截器設定的 Result 屬性命令執行之後 (其中一種...執行方法） 則 EF 會使用已變更的結果，而不是實際上從作業傳回的結果。 同樣地，如果命令執行之後，攔截器會將例外狀況屬性，然後 EF 會擲回組例外狀況，如果作業已擲回例外狀況。  

攔截器也可以設定為 null 表示應該擲回任何例外狀況的例外狀況屬性。 如果執行的作業失敗，但攔截器希望繼續像作業已成功的 EF，這非常有用。 這通常也牽涉到設定的結果，好讓 EF 必須持續使用一些結果值。  

#### <a name="originalresult-and-originalexception"></a>OriginalResult 和 OriginalException  

EF 執行作業之後它會設定 結果 和 OriginalResult 屬性如果執行未失敗或例外狀況和 OriginalException 屬性，如果執行失敗，發生例外狀況。  

OriginalResult 和 OriginalException 屬性是唯讀，並在實際執行作業之後才設定 EF 所。 無法設定這些屬性，由攔截器。 這表示任何攔截器可以區別例外狀況或已經設定了某些其他攔截器而不是實際的例外狀況的結果或執行作業時所發生的結果。  

### <a name="registering-interceptors"></a>註冊攔截器  

一旦建立實作一或多個攔截介面的類別，它可以向 EF 使用 DbInterception 類別。 例如:   

``` csharp
DbInterception.Add(new NLogCommandInterceptor());
```  

攔截器也可以註冊在應用程式定義域層級使用 DbConfiguration 程式碼為基礎的組態機制。  

### <a name="example-logging-to-nlog"></a>範例： 記錄 NLog  

讓我們將所有此一起範例的方法使用 IDbCommandInterceptor 並[NLog](http://nlog-project.org/)來：  

- 記錄非以非同步的方式執行的任何命令的警告  
- 記錄檔執行時，會擲回的任何命令的錯誤  

以下是類別的程式碼的記錄，它應該註冊，如上所示：  

``` csharp
public class NLogCommandInterceptor : IDbCommandInterceptor
{
    private static readonly Logger Logger = LogManager.GetCurrentClassLogger();

    public void NonQueryExecuting(
        DbCommand command, DbCommandInterceptionContext<int> interceptionContext)
    {
        LogIfNonAsync(command, interceptionContext);
    }

    public void NonQueryExecuted(
        DbCommand command, DbCommandInterceptionContext<int> interceptionContext)
    {
        LogIfError(command, interceptionContext);
    }

    public void ReaderExecuting(
        DbCommand command, DbCommandInterceptionContext<DbDataReader> interceptionContext)
    {
        LogIfNonAsync(command, interceptionContext);
    }

    public void ReaderExecuted(
        DbCommand command, DbCommandInterceptionContext<DbDataReader> interceptionContext)
    {
        LogIfError(command, interceptionContext);
    }

    public void ScalarExecuting(
        DbCommand command, DbCommandInterceptionContext<object> interceptionContext)
    {
        LogIfNonAsync(command, interceptionContext);
    }

    public void ScalarExecuted(
        DbCommand command, DbCommandInterceptionContext<object> interceptionContext)
    {
        LogIfError(command, interceptionContext);
    }

    private void LogIfNonAsync<TResult>(
        DbCommand command, DbCommandInterceptionContext<TResult> interceptionContext)
    {
        if (!interceptionContext.IsAsync)
        {
            Logger.Warn("Non-async command used: {0}", command.CommandText);
        }
    }

    private void LogIfError<TResult>(
        DbCommand command, DbCommandInterceptionContext<TResult> interceptionContext)
    {
        if (interceptionContext.Exception != null)
        {
            Logger.Error("Command {0} failed with exception {1}",
                command.CommandText, interceptionContext.Exception);
        }
    }
}
```  

請注意，此程式碼如何使用攔截內容，來探索非以非同步方式執行命令時，以及探索時執行命令時發生錯誤。  
