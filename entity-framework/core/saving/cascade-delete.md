---
title: 串聯刪除 - EF Core
description: 設定當實體從其主體/父系刪除或中斷時，所觸發的串聯行為
author: ajcvickers
ms.date: 01/07/2021
uid: core/saving/cascade-delete
ms.openlocfilehash: d6d40159fa17e9237bc6d5ece1b7f3670d638bbe
ms.sourcegitcommit: 196ebb726d99c2fa3f702d599f4bdae5e938cb1f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2021
ms.locfileid: "106165893"
---
# <a name="cascade-delete"></a>串聯刪除

Entity Framework Core (EF Core) 代表使用外鍵的關聯性。 具有外鍵的實體是關聯性中的子系或相依實體。 此實體的外鍵值必須符合相關主體/父實體的主鍵值 (或替代索引鍵值) 。

如果刪除主體/父系實體，則相依項/子系的外鍵值將不再符合 _任何_ 主體/父系的主要或替代索引鍵。 這是不正確狀態，而且在大多數資料庫中都會導致參考條件約束違規。

有兩個選項可避免這種參考條件約束違規：

1. 將 FK 值設定為 null
2. 也刪除相依/子系實體

第一個選項只適用于外鍵屬性 (的選擇性關聯性，以及它所對應的資料庫資料行) 必須是可為 null。

第二個選項適用于任何種類的關聯性，也稱為「串聯刪除」。

> [!TIP]
> 本檔說明串聯刪除 (，以及從更新資料庫的觀點來刪除孤立的) 。 它會大量使用 [變更追蹤中](xref:core/change-tracking/index) 引入的概念，EF Core 及 [變更外鍵和](xref:core/change-tracking/relationship-changes)導覽。 在這裡處理材質之前，請務必完整瞭解這些概念。

> [!TIP]  
> 您可以 [從 GitHub 下載範例程式碼](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/CascadeDeletes)，以執行並偵測到本檔中的所有程式碼。

## <a name="when-cascading-behaviors-happen"></a>當級聯行為發生時

當相依/子系實體無法再與其目前的主體/父系相關聯時，就需要串聯刪除。 這可能是因為主體/父系已刪除，或者當主體/父系仍然存在但相依/子系不再與其相關聯時，就會發生這種情況。

### <a name="deleting-a-principalparent"></a>刪除主體/父系

請考慮這個簡單的模型 `Blog` ，其中是與關聯性的主體/父系 `Post` ，也就是相依/子系。 `Post.BlogId` 這是外鍵屬性，其值必須符合 `Post.Id` blog 所屬之 post 的主要索引鍵。

<!--
    public class Blog
    {
        public int Id { get; set; }

        public string Name { get; set; }

        public IList<Post> Posts { get; } = new List<Post>();
    }

    public class Post
    {
        public int Id { get; set; }

        public string Title { get; set; }
        public string Content { get; set; }

        public int BlogId { get; set; }
        public Blog Blog { get; set; }
    }
-->
[!code-csharp[Model](../../../samples/core/CascadeDeletes/IntroRequiredSamples.cs?name=Model)]

依照慣例，此關聯性會設定為必要項，因為 `Post.BlogId` 外鍵屬性不可為 null。 必要的關聯性會設定為預設使用串聯刪除。 如需模型關聯性的詳細資訊，請參閱 [關聯](xref:core/modeling/relationships) 性。

刪除 blog 時，所有貼文都會進行串聯刪除。 例如：

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            context.Remove(blog);
            
            context.SaveChanges();
-->
[!code-csharp[Deleting_principal_parent_1](../../../samples/core/CascadeDeletes/IntroRequiredSamples.cs?name=Deleting_principal_parent_1)]

SaveChanges 會使用 SQL Server 作為範例，產生下列 SQL：

```sql
-- Executed DbCommand (1ms) [Parameters=[@p0='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Posts]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;

-- Executed DbCommand (0ms) [Parameters=[@p0='2'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Posts]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;

-- Executed DbCommand (2ms) [Parameters=[@p1='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Blogs]
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;
```

### <a name="severing-a-relationship"></a>切斷關聯性

我們可以改為在每個貼文與其 blog 之間的關聯性，而不是刪除 blog。 這可以藉由將 `Post.Blog` 每一篇文章的參考導覽設定為 null 來完成：

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            foreach (var post in blog.Posts)
            {
                post.Blog = null;
            }
            
            context.SaveChanges();
-->
[!code-csharp[Severing_a_relationship_1](../../../samples/core/CascadeDeletes/IntroRequiredSamples.cs?name=Severing_a_relationship_1)]

您也可以藉由移除集合導覽中的每個貼文來切斷關聯性 `Blog.Posts` ：

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            blog.Posts.Clear();
            
            context.SaveChanges();
-->
[!code-csharp[Severing_a_relationship_2](../../../samples/core/CascadeDeletes/IntroRequiredSamples.cs?name=Severing_a_relationship_2)]

在任一種情況下，結果都是一樣的：不會刪除 blog，但不再與任何 blog 相關聯的貼文會被刪除：

```sql
-- Executed DbCommand (1ms) [Parameters=[@p0='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Posts]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;

-- Executed DbCommand (0ms) [Parameters=[@p0='2'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Posts]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;
```

刪除不再與任何主體/相依相關聯的實體就稱為「刪除孤立」。

> [!TIP]
> 串聯刪除和刪除孤立的緊密相關。 這兩種方式都會在中斷與所需主體/父系的關聯性時，刪除相依/子系實體。 若是串聯刪除，則會發生此切斷，是因為主體/父系本身已被刪除。 若為孤立，主體/父實體仍存在，但不再與相依/子系實體相關。  

## <a name="where-cascading-behaviors-happen"></a>發生串聯行為的情況

串聯行為可套用至：

- 目前所追蹤的實體 <xref:Microsoft.EntityFrameworkCore.DbContext>
- 資料庫中尚未載入至內容的實體

### <a name="cascade-delete-of-tracked-entities"></a>已追蹤實體的 Cascade 刪除

EF Core 一律會將設定的串聯行為套用至追蹤的實體。 這表示，如果應用程式將所有相關的相依/子系實體載入 DbCoNtext （如上述範例所示），則不論資料庫的設定方式為何，都會正確套用串聯行為。

> [!TIP]
> 當追蹤實體發生串聯行為時的確切時間，可以使用和來 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeDeleteTiming?displayProperty=nameWithType> 控制 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DeleteOrphansTiming?displayProperty=nameWithType> 。 如需詳細資訊，請參閱 [變更外鍵和](xref:core/change-tracking/relationship-changes) 導覽。

### <a name="cascade-delete-in-the-database"></a>資料庫中的 Cascade delete

許多資料庫系統也會提供在資料庫中刪除實體時所觸發的串聯行為。 當使用或 EF Core 遷移來建立資料庫時，EF Core 會根據 EF Core 模型中的串聯刪除行為來設定這些行為 <xref:Microsoft.EntityFrameworkCore.Infrastructure.DatabaseFacade.EnsureCreated%2A> 。 例如，使用上述模型時，會在使用 SQL Server 時，為貼文建立下表：

```sql
CREATE TABLE [Posts] (
    [Id] int NOT NULL IDENTITY,
    [Title] nvarchar(max) NULL,
    [Content] nvarchar(max) NULL,
    [BlogId] int NOT NULL,
    CONSTRAINT [PK_Posts] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Posts_Blogs_BlogId] FOREIGN KEY ([BlogId]) REFERENCES [Blogs] ([Id]) ON DELETE CASCADE
);
```

請注意，定義 blog 和 post 之間關聯性的 foreign key 條件約束是使用來設定的 `ON DELETE CASCADE` 。

如果知道資料庫的設定就像這樣，則可以刪除 _沒有先載入貼_ 文的 blog，資料庫將負責刪除與該 blog 相關的所有文章。 例如：

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).First();

            context.Remove(blog);
            
            context.SaveChanges();
-->
[!code-csharp[Where_cascading_behaviors_happen_1](../../../samples/core/CascadeDeletes/IntroRequiredSamples.cs?name=Where_cascading_behaviors_happen_1)]

請注意，沒有 `Include` 貼文，因此不會載入。 在此情況下，SaveChanges 只會刪除 blog，因為這是唯一正在追蹤的實體：

```sql
-- Executed DbCommand (6ms) [Parameters=[@p0='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Blogs]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;
```

如果資料庫中的外鍵條件約束未設定成串聯刪除，這會導致例外狀況。 不過，在此情況下，資料庫會刪除貼文，因為它在 `ON DELETE CASCADE` 建立時已設定。

> [!NOTE]
> 資料庫通常不會有任何方法可以自動刪除孤立。 這是因為當 EF Core 使用導覽和外鍵來表示關聯性時，資料庫只會有外鍵和沒有導覽。 這表示，通常不可能在不將雙方載入至 DbCoNtext 的情況下，伺服器關聯性。

> [!NOTE]
> 記憶體內部資料庫 EF Core 目前不支援資料庫中的串聯刪除。

> [!WARNING]
> 當虛刪除實體時，請勿在資料庫中設定串聯刪除。 這可能會導致意外刪除實體，而不是虛刪除。

### <a name="database-cascade-limitations"></a>資料庫串聯限制

某些資料庫（尤其是 SQL Server）對於形成迴圈的串聯行為有限制。 例如，請考慮下列模型：

<!--
    public class Blog
    {
        public int Id { get; set; }
        public string Name { get; set; }

        public IList<Post> Posts { get; } = new List<Post>();
        
        public int OwnerId { get; set; }
        public Person Owner { get; set; }
    }

    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }

        public int BlogId { get; set; }
        public Blog Blog { get; set; }
        
        public int AuthorId { get; set; }
        public Person Author { get; set; }
    }

    public class Person
    {
        public int Id { get; set; }
        public string Name { get; set; }
        
        public IList<Post> Posts { get; } = new List<Post>();

        public Blog OwnedBlog { get; set; }
    }
-->
[!code-csharp[Model](../../../samples/core/CascadeDeletes/WithDatabaseCycleSamples.cs?name=Model)]

此模型有三個關聯性，全都是必要的，因此設定為依慣例進行串聯刪除：

- 刪除 blog 將會串聯刪除所有相關文章
- 刪除貼文的作者將會導致撰寫的貼文被刪除
- 刪除 blog 的擁有者會導致將 blog 刪除

如果在 blog 管理原則中定下嚴格，這是很合理的 (！ ) 但嘗試建立已設定這些級聯的 SQL Server 資料庫會導致下列例外狀況：

> SqlClient. SqlException (0x80131904) ：在資料表 ' Post ' 上導入 FOREIGN KEY 條件約束 ' FK_Posts_Person_AuthorId ' 可能會造成迴圈或多個串聯路徑。 請指定 ON DELETE NO ACTION 或 ON UPDATE NO ACTION，或者修改其他 FOREIGN KEY 條件約束。

有兩種方式可以處理這種情況：

1. 將一或多個關聯性變更為 [不串聯刪除]。
2. 設定不含一或多個這些串聯刪除的資料庫，然後確定已載入所有相依的實體，使 EF Core 可以執行串聯行為。

利用我們的範例採用第一種方法，我們可以為它提供可為 null 的外鍵屬性，讓您可以選擇建立 blog 擁有者關係：

<!--
            public int? BlogId { get; set; }
-->
[!code-csharp[NullableBlogId](../../../samples/core/CascadeDeletes/OptionalDependentsSamples.cs?name=NullableBlogId)]

選擇性的關聯性可讓 blog 在沒有擁有者的情況下存在，這表示預設不會再設定串聯刪除。 這表示串聯動作中不再有迴圈，而且可以在 SQL Server 上建立資料庫，而不會發生錯誤。

改為採用第二種方法，我們可以保持所需的 blog 擁有者關係並設定串聯刪除，但讓此設定只適用于追蹤的實體，而不是資料庫：

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder
                .Entity<Blog>()
                .HasOne(e => e.Owner)
                .WithOne(e => e.OwnedBlog)
                .OnDelete(DeleteBehavior.ClientCascade);
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/CascadeDeletes/WithDatabaseCycleSamples.cs?name=OnModelCreating)]

現在，如果我們同時載入個人和他們所擁有的 blog，就會發生什麼事，然後刪除該人？

<!--
            using var context = new BlogsContext();

            var owner = context.People.Single(e => e.Name == "ajcvickers");
            var blog = context.Blogs.Single(e => e.Owner == owner);

            context.Remove(owner);
            
            context.SaveChanges();
-->
[!code-csharp[Database_cascade_limitations_1](../../../samples/core/CascadeDeletes/WithDatabaseCycleSamples.cs?name=Database_cascade_limitations_1)]

EF Core 會將擁有者的刪除重迭，因此也會刪除該 blog：

```sql
-- Executed DbCommand (8ms) [Parameters=[@p0='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Blogs]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;

-- Executed DbCommand (2ms) [Parameters=[@p1='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [People]
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;
```

但是，如果在刪除擁有者時未載入 blog：

<!--
                using var context = new BlogsContext();

                var owner = context.People.Single(e => e.Name == "ajcvickers");

                context.Remove(owner);
            
                context.SaveChanges();
-->
[!code-csharp[Database_cascade_limitations_2](../../../samples/core/CascadeDeletes/WithDatabaseCycleSamples.cs?name=Database_cascade_limitations_2)]

然後會擲回例外狀況，因為資料庫中的 foreign key 條件約束違規：

> SqlClient. SqlException： DELETE 子句與參考條件約束 "FK_Blogs_People_OwnerId" 衝突。 衝突發生在資料庫 "臨時" 中，資料表 "dbo。Blog "，資料行 ' OwnerId '。
陳述式已經結束。

## <a name="cascading-nulls"></a>串聯 null

選擇性關聯性具有可為 null 的外鍵屬性對應至可為 null 的資料庫資料行。 這表示，當目前的主體/父系已刪除或從相依/子系中斷時，外鍵值可以設定為 null。

讓我們再次看看 [發生串聯行為時](#when-cascading-behaviors-happen)的範例，但這次具有以可為 null 的外鍵屬性工作表示的選擇性關聯性 `Post.BlogId` ：

<!--
            public int? BlogId { get; set; }
-->
[!code-csharp[NullableBlogId](../../../samples/core/CascadeDeletes/OptionalDependentsSamples.cs?name=NullableBlogId)]

當每個貼文的相關 blog 刪除時，此外鍵屬性將會設為 null。 例如，此程式碼與之前相同：

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            context.Remove(blog);
            
            context.SaveChanges();
-->
[!code-csharp[Deleting_principal_parent_1b](../../../samples/core/CascadeDeletes/IntroOptionalSamples.cs?name=Deleting_principal_parent_1b)]

現在會在呼叫 SaveChanges 時產生下列資料庫更新：

```sql
-- Executed DbCommand (2ms) [Parameters=[@p1='1', @p0=NULL (DbType = Int32)], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
UPDATE [Posts] SET [BlogId] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;

-- Executed DbCommand (0ms) [Parameters=[@p1='2', @p0=NULL (DbType = Int32)], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
UPDATE [Posts] SET [BlogId] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;

-- Executed DbCommand (1ms) [Parameters=[@p2='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Blogs]
WHERE [Id] = @p2;
SELECT @@ROWCOUNT;
```

同樣地，如果使用上述其中一個範例來切斷關聯性：

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            foreach (var post in blog.Posts)
            {
                post.Blog = null;
            }
            
            context.SaveChanges();
-->
[!code-csharp[Severing_a_relationship_1b](../../../samples/core/CascadeDeletes/IntroOptionalSamples.cs?name=Severing_a_relationship_1b)]

或：

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            blog.Posts.Clear();
            
            context.SaveChanges();
-->
[!code-csharp[Severing_a_relationship_2b](../../../samples/core/CascadeDeletes/IntroOptionalSamples.cs?name=Severing_a_relationship_2b)]

然後，當呼叫 SaveChanges 時，會使用 null 外鍵值更新貼文：

```sql
-- Executed DbCommand (2ms) [Parameters=[@p1='1', @p0=NULL (DbType = Int32)], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
UPDATE [Posts] SET [BlogId] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;

-- Executed DbCommand (0ms) [Parameters=[@p1='2', @p0=NULL (DbType = Int32)], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
UPDATE [Posts] SET [BlogId] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;
```

如需 EF Core 如何管理外鍵和導覽的詳細資訊，請參閱 [變更外鍵和](xref:core/change-tracking/relationship-changes) 導覽。

> [!NOTE]
> 這類關聯性的修復是自2008的第一個版本起 Entity Framework 的預設行為。 在 EF Core 之前，它沒有名稱，也不能變更。 現在稱為 `ClientSetNull` 下一節中所述。

當您刪除選擇性關聯性中的主體/父系時，也可以將資料庫設定為以如下方式串聯 null。 不過，這比使用資料庫中的串聯式刪除更不常見。 在資料庫中同時使用串聯刪除和串聯 null，幾乎一律會在使用 SQL Server 時產生關聯性迴圈。 如需設定串聯 null 的詳細資訊，請參閱下一節。

## <a name="configuring-cascading-behaviors"></a>設定串聯行為

> [!TIP]
> 在此之前，請務必閱讀上述各節。 如果無法理解先前的材質，設定選項可能不會有意義。

串聯行為是使用中的方法，為每個關聯性設定 <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.ReferenceCollectionBuilder.OnDelete%2A> <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> 。 例如：

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder
                .Entity<Blog>()
                .HasOne(e => e.Owner)
                .WithOne(e => e.OwnedBlog)
                .OnDelete(DeleteBehavior.ClientCascade);
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/CascadeDeletes/WithDatabaseCycleSamples.cs?name=OnModelCreating)]

如需設定實體類型之間關聯性的詳細資訊，請參閱 [關聯](xref:core/modeling/relationships) 性。

`OnDelete` 接受來自的值，不可否認的混淆， <xref:Microsoft.EntityFrameworkCore.DeleteBehavior> 列舉。 此列舉會定義追蹤實體上 EF Core 的行為，以及當 EF 用來建立架構時，資料庫中的串聯刪除的設定。

### <a name="impact-on-database-schema"></a>對資料庫架構的影響

下表顯示 `OnDelete` EF Core 遷移或的外鍵條件約束所建立的每個值的結果 <xref:Microsoft.EntityFrameworkCore.Infrastructure.DatabaseFacade.EnsureCreated%2A> 。

| Deletebehavior.restrict        | 對資料庫架構的影響
|:----------------------|--------------------------
| Cascade               | ON DELETE CASCADE
| 限制              | ON DELETE NO ACTION
| NoAction              | 資料庫預設值
| SetNull               | ON DELETE SET NULL
| ClientSetNull         | ON DELETE NO ACTION
| ClientCascade         | ON DELETE NO ACTION
| ClientNoAction        | 資料庫預設值

> [!NOTE]
> 此資料表很令人困惑，我們計畫在未來的版本中重新討論。 請參閱 [GitHub 問題 #21252](https://github.com/dotnet/efcore/issues/21252)。

`ON DELETE NO ACTION` `ON DELETE RESTRICT` 在關係資料庫中和的行為通常是完全相同或非常相似。 無論如何 `NO ACTION` ，這兩個選項都會導致強制執行參考條件約束。 如果有的話，差異就是資料庫檢查條件約束的 _時間_ 。  請檢查您的資料庫檔案，瞭解 `ON DELETE NO ACTION` 資料庫系統與之間的特定差異 `ON DELETE RESTRICT` 。

將在資料庫上造成串聯行為的唯一值為 `Cascade` 和 `SetNull` 。 所有其他值都會將資料庫設定為不串聯任何變更。

### <a name="impact-on-savechanges-behavior"></a>對 SaveChanges 行為的影響

下列各節中的表格說明當刪除主體/父系或其相依/子系實體的關聯性時，相依/子系實體會發生什麼事。 每個資料表都包含下列其中一個：

- 選擇性 (可為 null 的 FK) 和必要 (不可為 null 的 FK) 關聯性
- 當相依項/子系由 DbCoNtext 載入和追蹤，而且只存在於資料庫中時

#### <a name="required-relationship-with-dependentschildren-loaded"></a>已載入相依項/子系的必要關聯性

| Deletebehavior.restrict    | 刪除主體/父系時             | 從 principal/parent 切斷
|:------------------|------------------------------------------|----------------------------------------
| Cascade           | EF Core 刪除的相依項            | EF Core 刪除的相依項
| 限制          | `InvalidOperationException`              | `InvalidOperationException`
| NoAction          | `InvalidOperationException`              | `InvalidOperationException`
| SetNull           | `SqlException` 建立資料庫時      | `SqlException` 建立資料庫時
| ClientSetNull     | `InvalidOperationException`              | `InvalidOperationException`
| ClientCascade     | EF Core 刪除的相依項            | EF Core 刪除的相依項
| ClientNoAction    | `DbUpdateException`                      | `InvalidOperationException`

注意：

- 這類必要關聯性的預設值為 `Cascade` 。
- 針對必要關聯性使用 cascade delete 以外的任何其他作業，會在呼叫 SaveChanges 時產生例外狀況。
  - 一般來說，這是 `InvalidOperationException` 來自 EF Core，因為在載入的子系/相依項中偵測到不正確狀態。
  - `ClientNoAction` 強制 EF Core 不檢查修復相依項，然後再將它們傳送到資料庫，因此在此情況下，資料庫會擲回例外狀況，然後 `DbUpdateException` 由 SaveChanges 包裝在中。
  - `SetNull` 在建立資料庫時拒絕，因為外鍵資料行不可為 null。
- 因為相依項/子系已載入，所以 EF Core 一律會將其刪除，且永遠不會將資料庫刪除。

#### <a name="required-relationship-with-dependentschildren-not-loaded"></a>未載入相依項/子系的必要關聯性

| Deletebehavior.restrict    | 刪除主體/父系時             | 從 principal/parent 切斷
|:------------------|------------------------------------------|----------------------------------------
| Cascade           | 資料庫刪除的相依項           | N/A
| 限制          | `DbUpdateException`                      | N/A
| NoAction          | `DbUpdateException`                      | N/A
| SetNull           | `SqlException` 建立資料庫時      | N/A
| ClientSetNull     | `DbUpdateException`                      | N/A
| ClientCascade     | `DbUpdateException`                      | N/A
| ClientNoAction    | `DbUpdateException`                      | N/A

注意：

- 切斷關聯性在此無效，因為未載入相依項/子系。
- 這類必要關聯性的預設值為 `Cascade` 。
- 針對必要關聯性使用 cascade delete 以外的任何其他作業，會在呼叫 SaveChanges 時產生例外狀況。
  - 一般來說，這是 `DbUpdateException` 因為不會載入相依項/子系，因此只能由資料庫偵測到不正確狀態。 SaveChanges 接著會將資料庫例外狀況包裝在中 `DbUpdateException` 。
  - `SetNull` 在建立資料庫時拒絕，因為外鍵資料行不可為 null。

#### <a name="optional-relationship-with-dependentschildren-loaded"></a>已載入相依項/子系的選擇性關聯性

| Deletebehavior.restrict    | 刪除主體/父系時             | 從 principal/parent 切斷
|:------------------|------------------------------------------|----------------------------------------
| Cascade           | EF Core 刪除的相依項            | EF Core 刪除的相依項
| 限制          | 依 EF Core 將相依 Fk 設為 null     | 依 EF Core 將相依 Fk 設為 null
| NoAction          | 依 EF Core 將相依 Fk 設為 null     | 依 EF Core 將相依 Fk 設為 null
| SetNull           | 依 EF Core 將相依 Fk 設為 null     | 依 EF Core 將相依 Fk 設為 null
| ClientSetNull     | 依 EF Core 將相依 Fk 設為 null     | 依 EF Core 將相依 Fk 設為 null
| ClientCascade     | EF Core 刪除的相依項            | EF Core 刪除的相依項
| ClientNoAction    | `DbUpdateException`                      | 依 EF Core 將相依 Fk 設為 null

注意：

- 這類選擇性關聯性的預設值為 `ClientSetNull` 。
- 除非已 `Cascade` 設定或，否則不會刪除相依項/子系 `ClientCascade` 。
- 所有其他值都會將相依的 Fk 設為 null，EF Core .。。
  - ...但 `ClientNoAction` 在刪除主體/父系時，會告知 EF Core 不會接觸相依項/子系的外鍵。 因此，資料庫會擲回例外狀況，此例外狀況會 `DbUpdateException` 由 SaveChanges 包裝為。

#### <a name="optional-relationship-with-dependentschildren-not-loaded"></a>未載入相依項/子系的選擇性關聯性

| Deletebehavior.restrict    | 刪除主體/父系時             | 從 principal/parent 切斷
|:------------------|------------------------------------------|----------------------------------------
| Cascade           | 資料庫刪除的相依項           | N/A
| 限制          | `DbUpdateException`                      | N/A
| NoAction          | `DbUpdateException`                      | N/A
| SetNull           | 依資料庫將相依 Fk 設為 null    | N/A
| ClientSetNull     | `DbUpdateException`                      | N/A
| ClientCascade     | `DbUpdateException`                      | N/A
| ClientNoAction    | `DbUpdateException`                      | N/A

注意：

- 切斷關聯性在此無效，因為未載入相依項/子系。
- 這類選擇性關聯性的預設值為 `ClientSetNull` 。
- 必須載入相依項/子系，以避免發生資料庫例外狀況，除非資料庫已設定為串聯刪除或 null。
