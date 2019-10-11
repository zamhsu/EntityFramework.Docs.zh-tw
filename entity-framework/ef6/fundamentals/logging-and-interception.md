---
title: 記錄和攔截資料庫作業-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: b5ee7eb1-88cc-456e-b53c-c67e24c3f8ca
ms.openlocfilehash: 35b0284a5ad8b2b732f074589bd458d243312575
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72181663"
---
# <a name="logging-and-intercepting-database-operations"></a>記錄和攔截資料庫作業
> [!NOTE]
> **僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。 如果您使用的是較早版本，則不適用部分或全部的資訊。  

從 Entity Framework 6 開始，每當 Entity Framework 將命令傳送至資料庫時，應用程式代碼就可以攔截此命令。 這最常用於記錄 SQL，但也可以用來修改或中止命令。  

具體而言，EF 包含：  

- 類似 DataCoNtext 的內容的記錄檔屬性。請登入 LINQ to SQL  
- 自訂傳送至記錄之輸出內容和格式的機制  
- 適用于攔截的低層級建立區塊提供更大的控制/彈性  

## <a name="context-log-property"></a>內容記錄檔屬性  

您可以將 DbCoNtext 屬性設定為任何接受字串之方法的委派。 最常見的方法是將它設定為該不正確 "Write" 方法，以搭配任何不正確。 目前內容所產生的所有 SQL 都會記錄到該寫入器。 例如，下列程式碼會將 SQL 記錄到主控台：  

``` csharp
using (var context = new BlogContext())
{
    context.Database.Log = Console.Write;

    // Your code here...
}
```  

請注意，內容。Database .Log 已設定為 Console. Write。 這就是將 SQL 記錄到主控台所需的一切。  

讓我們新增一些簡單的查詢/插入/更新程式碼，讓我們可以看到一些輸出：  

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

（請注意，這是假設已經發生任何資料庫初始化的輸出。 如果尚未發生資料庫初始化，則會有更多的輸出顯示所有工作遷移會在幕後檢查或建立新的資料庫。）  

## <a name="what-gets-logged"></a>會記錄哪些專案？  

當 Log 屬性設定為時，將會記錄下列所有內容：  

- 適用于所有不同類型命令的 SQL。 例如:  
    - 查詢，包括一般 LINQ 查詢、eSQL 查詢，以及來自 SqlQuery 等方法的原始查詢  
    - 在 SaveChanges 中產生的插入、更新和刪除  
    - 關聯性載入查詢，例如消極式載入所產生的查詢  
- 參數  
- 是否要以非同步方式執行命令  
- 指出命令開始執行的時間戳記  
- 命令是否已成功完成、擲回例外狀況失敗，或已取消（針對非同步）  
- 結果值的某些指示  
- 執行命令所花費的大約時間量。 請注意，這是傳送命令以取回結果物件的時間。 不包含讀取結果的時間。  

查看上述的範例輸出，記錄的四個命令都是：  

- 呼叫內容所產生的查詢。Blog. First  
    - 請注意，取得 SQL 的 ToString 方法不會處理此查詢，因為「第一個」並未提供可呼叫 ToString 的 IQueryable  
- 從延遲載入的 blog 產生的查詢。回  
    - 請注意發生消極式載入之金鑰值的參數詳細資料。  
    - 只會記錄設定為非預設值之參數的屬性。 例如，只有在不是零的情況下，才會顯示 Size 屬性。  
- SaveChangesAsync 所產生的兩個命令;一個用於更新變更貼文標題，另一個用於插入以加入新的貼文  
    - 請注意 FK 和標題屬性的參數詳細資料  
    - 請注意，這些命令會以非同步方式執行  

## <a name="logging-to-different-places"></a>記錄到不同的位置  

如上所示，登入主控台非常簡單。 藉由使用不同種類的「類型」, 也很容易就能記錄到記憶體[檔案等等](https://msdn.microsoft.com/library/system.io.textwriter.aspx)、。  

如果您很熟悉 LINQ to SQL，您可能會注意到 LINQ to SQL 中的 Log 屬性設為實際的「無效」物件（例如，Console）。在 EF 中，Log 屬性會設定為可接受字串的方法（例如、Console. Write 或 Console。 這種情況的原因是要接受可做為字串接收的任何委派，以將 EF 與「解除耦合」分離。 例如，假設您已經有一些記錄架構，而且它會定義一種記錄方法，如下所示：  

``` csharp
public class MyLogger
{
    public void Log(string component, string message)
    {
        Console.WriteLine("Component: {0} Message: {1} ", component, message);
    }
}
```  

這可能會連接到 EF Log 屬性，如下所示：  

``` csharp
var logger = new MyLogger();
context.Database.Log = s => logger.Log("EFApp", s);
```  

## <a name="result-logging"></a>結果記錄  

預設記錄器會記錄命令文字（SQL）、參數和「執行中」行，並在命令傳送到資料庫之前加上時間戳記。 執行命令之後，會記錄包含經過時間的「已完成」行。  

請注意，針對非同步命令，在非同步工作實際完成、失敗或取消之前，不會記錄「已完成」行。  

「已完成」行包含不同的資訊，取決於命令的類型，以及執行是否成功。  

### <a name="successful-execution"></a>成功執行  

若為成功完成的命令，輸出會是「以 x 毫秒完成，結果為：」，後面會指出結果的意義。 對於傳回資料讀取器的命令，結果指示是傳回的[DbDataReader](https://msdn.microsoft.com/library/system.data.common.dbdatareader.aspx)類型。 對於傳回整數值的命令（如上方所示的 update 命令），會是該整數。  

### <a name="failed-execution"></a>執行失敗  

對於因擲回例外狀況而失敗的命令，輸出會包含來自例外狀況的訊息。 例如，使用 SqlQuery 來查詢存在的資料表，會導致記錄輸出如下所示：  

``` SQL
SELECT * from ThisTableIsMissing
-- Executing at 5/13/2013 10:19:05 AM
-- Failed in 1 ms with error: Invalid object name 'ThisTableIsMissing'.
```  

### <a name="canceled-execution"></a>已取消執行  

對於取消工作的非同步命令，結果可能會失敗併發生例外狀況，因為這是當嘗試取消時，基礎 ADO.NET 提供者通常會執行的動作。 如果沒有發生這種情況，而且工作已完全取消，則輸出看起來會像這樣：  

```console
update Blogs set Title = 'No' where Id = -1
-- Executing asynchronously at 5/13/2013 10:21:10 AM
-- Canceled in 1 ms
```  

## <a name="changing-log-content-and-formatting"></a>變更記錄檔內容和格式  

在中，資料庫 .Log 屬性會使用 DatabaseLogFormatter 物件。 此物件可有效地將 IDbCommandInterceptor 的實作為系結（如下所示）到接受字串和 DbCoNtext 的委派。 這表示在 EF 執行命令之前和之後，會呼叫 DatabaseLogFormatter 上的方法。 這些 DatabaseLogFormatter 方法會收集並設定記錄輸出的格式，並將它傳送至委派。  

### <a name="customizing-databaselogformatter"></a>自訂 DatabaseLogFormatter  

藉由建立衍生自 DatabaseLogFormatter 的新類別，並視需要覆寫方法，來變更記錄的內容和其格式化方式。 最常見的覆寫方法是：  

- LogCommand –覆寫此參數，以變更命令在執行之前的記錄方式。 根據預設，LogCommand 會針對每個參數呼叫 LogParameter;您可以選擇在覆寫中執行相同的動作，或改為以不同的方式處理參數。  
- LogResult –覆寫此參數，以變更如何記錄執行命令的結果。  
- LogParameter –覆寫此項以變更參數記錄的格式設定和內容。  

例如，假設我們想要在每個命令傳送至資料庫之前，只記錄一行。 這可以透過兩個覆寫來完成：  

- 覆寫 LogCommand 以格式化和寫入單一行的 SQL  
- 覆寫 LogResult 以不執行任何動作。  

程式碼看起來會像這樣：

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

若要記錄輸出，只要呼叫寫入方法，就會將輸出傳送至已設定的寫入委派。  

（請注意，此程式碼會簡單地移除分行符號，就像範例一樣。 這可能不適合用來查看複雜的 SQL）。  

### <a name="setting-the-databaselogformatter"></a>設定 DatabaseLogFormatter  

一旦建立新的 DatabaseLogFormatter 類別之後，就必須向 EF 註冊它。 這是使用以程式碼為基礎的設定來完成。 簡單來說，這表示要建立一個衍生自 DbConfiguration 的新類別（在與 DbCoNtext 類別相同的元件中），然後在這個新類別的函式中呼叫 SetDatabaseLogFormatter。 例如:  

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

這個新的 DatabaseLogFormatter 現在會在任何時候都使用 Database .Log。 因此，執行第1部分的程式碼現在會產生下列輸出：  

```console
Context 'BlogContext' is executing command 'SELECT TOP (1) [Extent1].[Id] AS [Id], [Extent1].[Title] AS [Title]FROM [dbo].[Blogs] AS [Extent1]WHERE (N'One Unicorn' = [Extent1].[Title]) AND ([Extent1].[Title] IS NOT NULL)'
Context 'BlogContext' is executing command 'SELECT [Extent1].[Id] AS [Id], [Extent1].[Title] AS [Title], [Extent1].[BlogId] AS [BlogId]FROM [dbo].[Posts] AS [Extent1]WHERE [Extent1].[BlogId] = @EntityKeyValue1'
Context 'BlogContext' is executing command 'update [dbo].[Posts]set [Title] = @0where ([Id] = @1)'
Context 'BlogContext' is executing command 'insert [dbo].[Posts]([Title], [BlogId])values (@0, @1)select [Id]from [dbo].[Posts]where @@rowcount > 0 and [Id] = scope_identity()'
```  

## <a name="interception-building-blocks"></a>攔截建立區塊  

到目前為止，我們已經討論過如何使用 DbCoNtext 來記錄 EF 所產生的 SQL。 但是，這段程式碼在某些較低層的建立區塊上其實是相當精簡的外觀，以便進行一般的攔截。  

### <a name="interception-interfaces"></a>攔截介面  

攔截程式碼是根據攔截介面的概念來建立的。 這些介面會繼承自 IDbInterceptor，並定義 EF 執行某個動作時所呼叫的方法。 其目的是要攔截每個物件類型的一個介面。 例如，IDbCommandInterceptor 介面會定義在 EF 呼叫 ExecuteNonQuery、ExecuteScalar、ExecuteReader 和相關方法之前呼叫的方法。 同樣地，介面會定義每個作業完成時所呼叫的方法。 我們在上方查看的 DatabaseLogFormatter 類別會執行此介面來記錄命令。  

### <a name="the-interception-context"></a>攔截內容  

查看在任何攔截器介面上定義的方法，很明顯地，每個呼叫都有一個類型為 DbInterceptionCoNtext 的物件，或是一個衍生自 this 的類型，例如 DbCommandInterceptionCoNtext @ no__t-0 @ no__t-1。 此物件包含 EF 正在採取之動作的相關內容資訊。 例如，如果是代表 DbCoNtext 來採取動作，則 DbCoNtext 會包含在 DbInterceptionCoNtext 中。 同樣地，針對以非同步方式執行的命令，會在 DbCommandInterceptionCoNtext 上設定 IsAsync 旗標。  

### <a name="result-handling"></a>結果處理  

DbCommandInterceptionCoNtext @ no__t-0 @ no__t-1 類別包含一個稱為 Result、OriginalResult、Exception 和 OriginalException 的屬性。 對於在執行作業之前呼叫的攔截方法呼叫，這些屬性會設定為 null/零，也就是 。執行方法。 如果作業已執行且成功，則 Result 和 OriginalResult 會設定為作業的結果。 然後可以在作業執行後呼叫的攔截方法中觀察這些值，也就是在 。已執行的方法。 同樣地，如果作業擲回，則會設定 Exception 和 OriginalException 屬性。  

#### <a name="suppressing-execution"></a>隱藏執行  

如果攔截器在命令執行之前設定 Result 屬性（在其中一個 。執行方法）之後，EF 就不會嘗試實際執行命令，而只會使用結果集。 換句話說，攔截器可以隱藏命令的執行，但讓 EF 繼續執行，就像命令已經執行一樣。  

如何使用此方法的範例，就是傳統上使用包裝提供者完成的命令批次處理。 攔截器會以批次方式儲存命令以供稍後執行，但會對 EF 「假設」命令已正常執行。 請注意，它需要的不只是用來執行批次處理，但這是可能會使用變更攔截結果的範例。  

您也可以藉由設定其中一個 ... 的 Exception 屬性來隱藏執行。執行方法。 這會導致 EF 繼續執行，就像是藉由擲回指定的例外狀況而失敗。 當然，這可能會造成應用程式當機，但也可能是暫時性的例外狀況，或是 EF 處理的其他例外狀況。 例如，這可以在測試環境中用來在命令執行失敗時測試應用程式的行為。  

#### <a name="changing-the-result-after-execution"></a>在執行後變更結果  

如果攔截器在命令執行後設定 Result 屬性（在其中一個 。執行的方法），則 EF 會使用變更的結果，而不是實際從作業傳回的結果。 同樣地，如果攔截器在命令執行後設定例外狀況屬性，則 EF 會擲回設定例外狀況，就像作業已擲回例外狀況一樣。  

攔截器也可以將例外狀況屬性設定為 null，表示不應該擲回任何例外狀況。 這在作業執行失敗時很有用，但攔截器希望 EF 繼續進行，如同作業已成功。 這通常也會牽涉到設定結果，使 EF 在繼續時有一些結果值可以使用。  

#### <a name="originalresult-and-originalexception"></a>OriginalResult 和 OriginalException  

在 EF 執行過作業之後，如果執行失敗，則會設定 Result 和 OriginalResult 屬性，如果執行失敗且發生例外狀況，則會設定例外狀況和 OriginalException 屬性。  

OriginalResult 和 OriginalException 屬性是唯讀的，而且只有在實際執行作業之後，才會由 EF 設定。 攔截器無法設定這些屬性。 這表示任何攔截器可以區別某些其他攔截器所設定的例外狀況或結果，而不是執行作業時所發生的實際例外狀況或結果。  

### <a name="registering-interceptors"></a>正在註冊攔截器  

一旦建立了一個或多個攔截介面的類別之後，就可以使用 DbInterception 類別向 EF 註冊它。 例如:  

``` csharp
DbInterception.Add(new NLogCommandInterceptor());
```  

攔截器也可以使用 DbConfiguration 程式碼型設定機制，在應用程式域層級註冊。  

### <a name="example-logging-to-nlog"></a>範例：記錄至 NLog  

讓我們將所有內容一起放入一個使用 IDbCommandInterceptor 和[NLog](https://nlog-project.org/)的範例：  

- 針對任何以非非同步方式執行的命令記錄警告  
- 針對任何在執行時擲回的命令記錄錯誤  

以下是執行記錄的類別，應該註冊，如上所示：  

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

請注意，此程式碼如何使用攔截內容來探索何時以非非同步方式執行命令，以及在執行命令時發生錯誤時進行探索。  
