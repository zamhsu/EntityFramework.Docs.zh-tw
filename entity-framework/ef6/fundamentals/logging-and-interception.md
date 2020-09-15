---
title: 記錄和攔截資料庫作業-EF6
description: 記錄和攔截 Entity Framework 6 中的資料庫作業
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/logging-and-interception
ms.openlocfilehash: 858e96a781933da52725efa56087bc27db06257d
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072664"
---
# <a name="logging-and-intercepting-database-operations"></a>記錄和攔截資料庫作業
> [!NOTE]
> **僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。 如果您使用的是較早版本，則不適用部分或全部的資訊。  

從 Entity Framework 6 開始，每次 Entity Framework 將命令傳送至資料庫時，應用程式程式碼可以攔截此命令。 這最常用於記錄 SQL，但是也可以用來修改或中止命令。  

具體而言，EF 包含：  

- 類似于 DataCoNtext 的內容記錄檔屬性 LINQ to SQL 中的記錄檔屬性  
- 一種機制，可自訂傳送至記錄檔之輸出的內容和格式  
- 適用于攔截的低層級建立區塊，提供更大的控制/彈性  

## <a name="context-log-property"></a>內容記錄檔屬性  

您可以針對任何採用字串的方法，將 DbCoNtext 屬性設定為委派。 最常見的方法是將它設定為該該配置器的「寫入」方法，以搭配任何的任何值來使用。 目前內容所產生的所有 SQL 都會記錄到該寫入器中。 例如，下列程式碼會將 SQL 記錄到主控台：  

``` csharp
using (var context = new BlogContext())
{
    context.Database.Log = Console.Write;

    // Your code here...
}
```  

請注意該內容。資料庫 .Log 設定為 Console. Write。 這是將 SQL 記錄到主控台所需的一切。  

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

 (請注意，這是假設已發生任何資料庫初始化的輸出。 如果尚未進行資料庫初始化，則會有更多輸出顯示所有的工作遷移都在檢查或建立新的資料庫。 )   

## <a name="what-gets-logged"></a>會記錄哪些專案？  

當記錄屬性設定完成時，將會記錄下列各項：  

- 適用于所有不同類型命令的 SQL。 例如：  
    - 查詢，包括一般 LINQ 查詢、eSQL 查詢，以及來自方法（例如 SqlQuery）的原始查詢  
    - 作為 SaveChanges 一部分產生的插入、更新和刪除  
    - 關聯性載入查詢，例如消極式載入所產生的查詢  
- 參數  
- 命令是否以非同步方式執行  
- 指出命令開始執行的時間戳記  
- 命令是否已順利完成、擲回例外狀況失敗，或已取消（非同步）  
- 結果值的某些指示  
- 執行命令所花費的大約時間量。 請注意，這是傳送命令以取回結果物件的時間。 它不包含讀取結果的時間。  

查看上述的範例輸出，每四個命令都會記錄：  

- 對內容的呼叫所產生的查詢。Blog。 First  
    - 請注意，取得 SQL 的 ToString 方法對此查詢沒有作用，因為 "First" 並未提供可呼叫 ToString 的 IQueryable。  
- 從延遲載入的 blog 產生的查詢。職位  
    - 請注意發生消極式載入的金鑰值的參數詳細資料  
    - 只會記錄設定為非預設值之參數的屬性。 例如，Size 屬性只有在非零的情況下才會顯示。  
- SaveChangesAsync 所產生的兩個命令;一個用於變更貼文標題的更新，另一個則用於插入以新增貼文  
    - 請注意 FK 和 Title 屬性的參數詳細資料  
    - 請注意，這些命令是以非同步方式執行  

## <a name="logging-to-different-places"></a>記錄到不同的位置  

如上所示，您可以輕鬆地登入主控台。 您也可以使用不同 [種類的配置](https://msdn.microsoft.com/library/system.io.textwriter.aspx)檔，輕鬆地記錄至記憶體、檔案等。  

如果您很熟悉 LINQ to SQL 您可能會注意到，LINQ to SQL 記錄檔屬性已設定為實際的預設值物件 (例如，主控台) 。在 EF 中，Log 屬性會設定為可接受字串的方法，例如，Console. Write 或 Console. Write (。 這種情況的原因是要藉由接受可作為字串接收的任何委派，來將 EF 與確定分離。 例如，假設您已經有一些記錄架構，它會定義如下的記錄方法：  

``` csharp
public class MyLogger
{
    public void Log(string component, string message)
    {
        Console.WriteLine("Component: {0} Message: {1} ", component, message);
    }
}
```  

這可能會連結至 EF 記錄檔屬性，如下所示：  

``` csharp
var logger = new MyLogger();
context.Database.Log = s => logger.Log("EFApp", s);
```  

## <a name="result-logging"></a>結果記錄  

預設記錄器會記錄命令文字 (SQL) 、參數，以及將命令傳送至資料庫之前，具有時間戳記的「執行中」行。 執行命令後，會記錄包含經過時間的「已完成」行。  

請注意，針對非同步命令，在非同步工作實際完成、失敗或取消之前，不會記錄「已完成」行。  

「已完成」行包含不同的資訊，這取決於命令類型以及是否成功執行。  

### <a name="successful-execution"></a>成功執行  

若為順利完成的命令，輸出會是「在 x 毫秒內完成，並產生結果：」，後面接著某個結果的指示。 針對傳回資料讀取器的命令，結果指示是傳回的 [DbDataReader](https://msdn.microsoft.com/library/system.data.common.dbdatareader.aspx) 類型。 針對傳回整數值的命令，例如上面所示的 update 命令，結果就是該整數。  

### <a name="failed-execution"></a>失敗的執行  

針對因擲回例外狀況而失敗的命令，輸出會包含來自例外狀況的訊息。 例如，使用 SqlQuery 針對存在的資料表進行查詢，將會導致記錄輸出，如下所示：  

``` SQL
SELECT * from ThisTableIsMissing
-- Executing at 5/13/2013 10:19:05 AM
-- Failed in 1 ms with error: Invalid object name 'ThisTableIsMissing'.
```  

### <a name="canceled-execution"></a>已取消執行  

對於取消工作的非同步命令，結果可能會因例外狀況而失敗，因為這是基礎 ADO.NET 提供者在嘗試取消時通常會執行的作業。 如果這不會發生，而且工作已完全取消，則輸出看起來會像這樣：  

```console
update Blogs set Title = 'No' where Id = -1
-- Executing asynchronously at 5/13/2013 10:21:10 AM
-- Canceled in 1 ms
```  

## <a name="changing-log-content-and-formatting"></a>變更記錄內容和格式  

在中，Database .Log 屬性會使用 DatabaseLogFormatter 物件。 這個物件會有效地系結 IDbCommandInterceptor 的實 (請參閱以下) 到接受字串和 DbCoNtext 的委派。 這表示 DatabaseLogFormatter 上的方法會在 EF 執行命令之前和之後呼叫。 這些 DatabaseLogFormatter 方法會收集並格式化記錄輸出，並將其傳送給委派。  

### <a name="customizing-databaselogformatter"></a>自訂 DatabaseLogFormatter  

您可以建立衍生自 DatabaseLogFormatter 的新類別，並視需要覆寫方法，以變更所記錄的內容和格式。 最常見的覆寫方法如下：  

- LogCommand –覆寫此參數以變更命令在執行之前的記錄方式。 依預設，LogCommand 會呼叫每個參數的 LogParameter;您可以選擇在覆寫中進行相同的動作，或改以不同的方式處理參數。  
- LogResult –覆寫此項，以變更如何記錄執行命令的結果。  
- LogParameter –覆寫此參數以變更參數記錄的格式和內容。  

例如，假設我們想要在每個命令傳送至資料庫之前，只記錄一行。 這可以透過兩個覆寫來完成：  

- 覆寫 LogCommand 以格式化及撰寫單一的 SQL 行  
- 覆寫 LogResult 不執行任何動作。  

程式碼應該類似如下所示：

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

若要記錄輸出，只要呼叫寫入方法，將輸出傳送至設定的寫入委派即可。  

 (請注意，此程式碼會簡化分行符號的移除，就像範例一樣。 它可能不適用於觀看複雜的 SQL。 )   

### <a name="setting-the-databaselogformatter"></a>設定 DatabaseLogFormatter  

建立新的 DatabaseLogFormatter 類別之後，必須向 EF 註冊。 這是使用以程式碼為基礎的設定來完成。 總而言之，這表示建立新的類別，該類別衍生自 >dbconfiguration 在相同元件中的 DbCoNtext 類別，然後在這個新類別的函式中呼叫 SetDatabaseLogFormatter。 例如：  

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

這個新的 DatabaseLogFormatter 現在會在每次設定資料庫時使用。 因此，從第1部分執行程式碼現在會產生下列輸出：  

```console
Context 'BlogContext' is executing command 'SELECT TOP (1) [Extent1].[Id] AS [Id], [Extent1].[Title] AS [Title]FROM [dbo].[Blogs] AS [Extent1]WHERE (N'One Unicorn' = [Extent1].[Title]) AND ([Extent1].[Title] IS NOT NULL)'
Context 'BlogContext' is executing command 'SELECT [Extent1].[Id] AS [Id], [Extent1].[Title] AS [Title], [Extent1].[BlogId] AS [BlogId]FROM [dbo].[Posts] AS [Extent1]WHERE [Extent1].[BlogId] = @EntityKeyValue1'
Context 'BlogContext' is executing command 'update [dbo].[Posts]set [Title] = @0where ([Id] = @1)'
Context 'BlogContext' is executing command 'insert [dbo].[Posts]([Title], [BlogId])values (@0, @1)select [Id]from [dbo].[Posts]where @@rowcount > 0 and [Id] = scope_identity()'
```  

## <a name="interception-building-blocks"></a>攔截建立區塊  

到目前為止，我們已經討論過如何使用 DbCoNtext 來記錄 EF 所產生的 SQL。 但在某些低層級的建立區塊中，此程式碼實際上是相當精簡的外觀，以進行一般攔截。  

### <a name="interception-interfaces"></a>攔截介面  

攔截程式碼是圍繞攔截介面的概念所建立。 這些介面繼承自 IDbInterceptor，並定義 EF 執行某些動作時所呼叫的方法。 其目的是要攔截的每個物件類型都有一個介面。 例如，IDbCommandInterceptor 介面會定義在 EF 呼叫 ExecuteNonQuery、ExecuteScalar、ExecuteReader 和相關方法之前所呼叫的方法。 同樣地，介面會定義在每個作業完成時所呼叫的方法。 我們在上面探討的 DatabaseLogFormatter 類別會執行此介面來記錄命令。  

### <a name="the-interception-context"></a>攔截內容  

查看任何攔截器介面上定義的方法，很明顯地，每個呼叫都有一個 DbInterceptionCoNtext 類型的物件，或是一些衍生自這個類別的型別，例如 DbCommandInterceptionCoNtext \<\> 。 此物件包含 EF 所採取之動作的相關內容資訊。 例如，如果正在代表 DbCoNtext 執行動作，則 DbCoNtext 會包含在 DbInterceptionCoNtext 中。 同樣地，對於要以非同步方式執行的命令，IsAsync 旗標是在 DbCommandInterceptionCoNtext 上設定的。  

### <a name="result-handling"></a>結果處理  

DbCommandInterceptionCoNtext \<\> 類別包含一個稱為 Result、OriginalResult、Exception 和 OriginalException 的屬性。 這些屬性會設定為 null/零，用於呼叫在執行作業之前呼叫的攔截方法，也就是 .。。正在執行方法。 如果作業已執行且成功，則結果和 OriginalResult 會設定為作業的結果。 然後，您可以在作業執行後呼叫的攔截方法中觀察到這些值（也就是在 .。。執行的方法。 同樣地，如果作業擲回，則會設定例外狀況和 OriginalException 屬性。  

#### <a name="suppressing-execution"></a>隱藏執行  

如果攔截器設定結果屬性，然後命令已執行 (在其中一個 .。。執行方法) 接著 EF 不會嘗試實際執行命令，而只會使用結果集。 換句話說，攔截器可以隱藏命令的執行，但讓 EF 繼續執行，就像命令已執行一樣。  

使用這個方法的範例，是傳統上使用包裝提供者完成的命令批次處理。 攔截器會以批次方式儲存命令以供稍後執行，但會將命令「假裝」為 EF，命令已正常執行。 請注意，它需要的不只是為了執行批次處理，但這是可能使用變更攔截結果的範例。  

您也可以藉由設定其中一個 .。。正在執行方法。 這會導致 EF 藉由擲回指定的例外狀況而繼續執行作業失敗。 當然，這可能會導致應用程式損毀，但也可能是暫時性例外狀況，或是 EF 所處理的其他例外狀況。 例如，這可以在測試環境中用來測試命令執行失敗時的應用程式行為。  

#### <a name="changing-the-result-after-execution"></a>在執行之後變更結果  

如果攔截器在命令執行之後設定 Result 屬性 (在其中一個 .。。執行的方法) 接著 EF 會使用變更的結果，而不是實際從作業傳回的結果。 同樣地，如果攔截器在命令執行之後設定 Exception 屬性，EF 將擲回 set 例外狀況，就像作業擲回例外狀況一樣。  

攔截器也可以將 Exception 屬性設為 null，表示不應該擲回例外狀況。 如果執行作業失敗，但攔截器希望 EF 繼續，就像作業成功一樣，這會很有用。 這通常也會牽涉到設定結果，讓 EF 有一些結果值在繼續時仍能運作。  

#### <a name="originalresult-and-originalexception"></a>OriginalResult 和 OriginalException  

在 EF 執行作業之後，它會在執行失敗時設定結果和 OriginalResult 屬性，如果執行失敗且發生例外狀況，則會設定例外狀況和 OriginalException 屬性。  

OriginalResult 和 OriginalException 屬性是唯讀的，而且只會在實際執行作業之後由 EF 設定。 攔截器無法設定這些屬性。 這表示任何攔截器都可以區分某些其他攔截器已設定的例外狀況或結果，而不是執行作業時所發生的實際例外狀況或結果。  

### <a name="registering-interceptors"></a>註冊攔截器  

一旦建立了一個或多個攔截介面的類別之後，就可以使用 DbInterception 類別向 EF 註冊。 例如：  

``` csharp
DbInterception.Add(new NLogCommandInterceptor());
```  

您也可以使用 >dbconfiguration 程式碼式設定機制，在應用程式域層級註冊攔截器。  

### <a name="example-logging-to-nlog"></a>範例：記錄至 NLog  

讓我們將這些全部整合在一起的範例中，使用 IDbCommandInterceptor 和 [NLog](https://nlog-project.org/) ：  

- 針對任何以非非同步方式執行的命令記錄警告  
- 記錄執行時擲回之任何命令的錯誤  

以下是執行記錄的類別，應該註冊如下所示：  

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

請注意，此程式碼如何使用攔截內容來探索何時以非非同步方式執行命令，並在執行命令時發生錯誤時探索。  
