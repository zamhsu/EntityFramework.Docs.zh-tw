---
title: 明確追蹤實體-EF Core
description: 使用 [新增]、[附加]、[更新] 和 [移除] 以 DbCoNtext 明確追蹤實體
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/explicit-tracking
ms.openlocfilehash: 28a6ec3e3c25dad70882b8681f78744a5979efe6
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129707"
---
# <a name="explicitly-tracking-entities"></a>明確追蹤實體

每個 <xref:Microsoft.EntityFrameworkCore.DbContext> 實例都會追蹤對實體所做的變更。 接著，這些追蹤的實體會在呼叫時驅動資料庫的變更 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> 。

Entity Framework Core (EF Core) 當相同的 <xref:Microsoft.EntityFrameworkCore.DbContext> 實例用於查詢實體，並藉由呼叫來更新時，變更追蹤的效果最佳。 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> 這是因為 EF Core 會自動追蹤查詢實體的狀態，然後在呼叫 SaveChanges 時，偵測對這些實體所做的任何變更。 這種方法會在 [EF Core 的變更追蹤中](xref:core/change-tracking/index)涵蓋。

> [!TIP]
> 本檔假設您已瞭解實體狀態以及 EF Core 變更追蹤的基本概念。 如需有關這些主題的詳細資訊，請參閱 [EF Core 中的變更追蹤](xref:core/change-tracking/index) 。

> [!TIP]
> 您可以 [從 GitHub 下載範例程式碼](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/ChangeTrackingInEFCore)，以執行並偵測到本檔中的所有程式碼。

> [!TIP]
> 為了簡單起見，本檔會使用和參考同步方法（例如）， <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> 而不是其非同步對應專案（例如） <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A> 。 除非另有說明，否則呼叫和等候 async 方法可以替代。

## <a name="introduction"></a>簡介

實體可以明確地「附加」到 <xref:Microsoft.EntityFrameworkCore.DbContext> ，讓內容接著追蹤這些實體。 這在下列情況下很有用：

1. 建立將插入資料庫的新實體。
2. 重新附加先前由 _不同_ DbCoNtext 實例查詢的已中斷連接實體。

大部分的應用程式都需要這些應用程式的第一個，而且是由方法所處理的主要 <xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> 。

只有在 **_未追蹤實體時_**，變更實體或其關聯性的應用程式需要第二個。 例如，web 應用程式可能會將實體傳送至 web 用戶端，讓使用者進行變更，並將實體送回。 這些實體稱為「已中斷連線」，因為它們最初是從 DbCoNtext 查詢，但在傳送至用戶端時，會從該內容中斷連線。

Web 應用程式現在必須重新附加這些實體，才能再次追蹤這些實體，並指出已進行的變更，以便對 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> 資料庫進行適當的更新。 這主要是由 <xref:Microsoft.EntityFrameworkCore.DbContext.Attach%2A?displayProperty=nameWithType> 和方法處理 <xref:Microsoft.EntityFrameworkCore.DbContext.Update%2A?displayProperty=nameWithType> 。

> [!TIP]
> 通常不需要將實體附加至其查詢來源的 _相同 DbCoNtext 實例_ 。 請勿定期執行無追蹤查詢，然後將傳回的實體附加至相同的內容。 這會比使用追蹤查詢更慢，而且也可能會導致問題（例如遺漏陰影屬性值），使其更難正確。

### <a name="generated-verses-explicit-key-values"></a>產生的辭句明確索引鍵值

依預設，會將整數和 GUID 索引 [鍵屬性](xref:core/modeling/keys) 設定為使用 [自動產生](xref:core/modeling/generated-properties)的索引鍵值。 這主要是 **變更追蹤的優點：未設定的索引鍵值表示實體為 "new"**。 「新增」表示它尚未插入資料庫中。

下列各節會使用兩個模型。 第一個設定為 **不** 使用產生的索引鍵值：

<!--
    public class Blog
    {
        [DatabaseGenerated(DatabaseGeneratedOption.None)]
        public int Id { get; set; }

        public string Name { get; set; }

        public IList<Post> Posts { get; } = new List<Post>();
    }

    public class Post
    {
        [DatabaseGenerated(DatabaseGeneratedOption.None)]
        public int Id { get; set; }

        public string Title { get; set; }
        public string Content { get; set; }

        public int? BlogId { get; set; }
        public Blog Blog { get; set; }
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Model)]

非產生的 (也就是明確設定) 的索引鍵值會先顯示在每個範例中，因為一切都非常明確且容易遵循。 接下來是使用產生的索引鍵值的範例：

<!--
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
    public string Content { get; set; }

    public int? BlogId { get; set; }
    public Blog Blog { get; set; }
}
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Model)]

請注意，此模型中的索引鍵屬性不需要其他設定，因為使用產生的索引鍵值是 [簡單整數索引鍵的預設](xref:core/modeling/generated-properties)值。

## <a name="inserting-new-entities"></a>插入新的實體

### <a name="explicit-key-values"></a>明確的索引鍵值

您必須在要插入的狀態中追蹤實體 `Added` <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> 。 實體通常會藉由呼叫 <xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> 、、 <xref:Microsoft.EntityFrameworkCore.DbContext.AddRange%2A?displayProperty=nameWithType> <xref:Microsoft.EntityFrameworkCore.DbContext.AddAsync%2A?displayProperty=nameWithType> 、 <xref:Microsoft.EntityFrameworkCore.DbContext.AddRangeAsync%2A?displayProperty=nameWithType> 或中的對等方法 <xref:Microsoft.EntityFrameworkCore.DbSet%601> 的其中一個來進入加入的狀態。

> [!TIP]
> 在變更追蹤的內容中，這些方法都是以相同的方式運作。 如需詳細資訊，請參閱 [其他變更追蹤功能](xref:core/change-tracking/miscellaneous) 。

例如，若要開始追蹤新的 blog：

<!--
            context.Add(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                });
-->
[!code-csharp[Inserting_new_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Inserting_new_entities_1)]

檢查此呼叫之後的 [變更追蹤器調試](xref:core/change-tracking/debug-views) 程式，會顯示內容正在追蹤狀態中的新實體 `Added` ：

```output
Blog {Id: 1} Added
  Id: 1 PK
  Name: '.NET Blog'
  Posts: []
```

不過，Add 方法不只是在個別實體上運作。 它們實際上會開始追蹤 _相關實體的整個圖形_，將它們全部放在 `Added` 狀態。 例如，若要插入新的 blog 和相關的新文章：

<!--
            context.Add(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Id = 1,
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Id = 2,
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        }
                    }
                });
-->
[!code-csharp[Inserting_new_entities_2](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Inserting_new_entities_2)]

內容現在會將這些實體追蹤為 `Added` ：

```output
Blog {Id: 1} Added
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Added
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Added
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

請注意，已針對 `Id` 上述範例中的索引鍵屬性設定明確值。 這是因為這裡的模型已設定為使用明確設定的索引鍵值，而不是自動產生的索引鍵值。 未使用產生的索引鍵時，必須在呼叫 _之前_ 明確設定索引鍵屬性 `Add` 。 然後在呼叫 SaveChanges 時插入這些索引鍵值。 例如，在使用 SQLite 時：

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String), @p1='.NET Blog' (Size = 9)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Blogs" ("Id", "Name")
VALUES (@p0, @p1);

-- Executed DbCommand (0ms) [Parameters=[@p2='1' (DbType = String), @p3='1' (DbType = String), @p4='Announcing the release of EF Core 5.0, a full featured cross-platform...' (Size = 72), @p5='Announcing the Release of EF Core 5.0' (Size = 37)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("Id", "BlogId", "Content", "Title")
VALUES (@p2, @p3, @p4, @p5);

-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String), @p1='1' (DbType = String), @p2='F# 5 is the latest version of F#, the functional programming language...' (Size = 72), @p3='Announcing F# 5' (Size = 15)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("Id", "BlogId", "Content", "Title")
VALUES (@p0, @p1, @p2, @p3);
```

在 SaveChanges 完成之後，這些實體都會在狀態中追蹤 `Unchanged` ，因為這些實體現在存在於資料庫中：

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

### <a name="generated-key-values"></a>產生的索引鍵值

如上所述，根據預設，會將整數和 GUID 索引 [鍵屬性](xref:core/modeling/keys) 設定為使用 [自動產生](xref:core/modeling/generated-properties) 的索引鍵值。 這表示應用程式 _不得明確地設定任何索引鍵值_。 例如，若要插入新的 blog，並以產生的金鑰值張貼所有文章：

<!--
            context.Add(
                new Blog
                {
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        }
                    }
                });
-->
[!code-csharp[Inserting_new_entities_3](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Inserting_new_entities_3)]

如同明確的索引鍵值，內容現在會將這些實體追蹤為 `Added` ：

```output
Blog {Id: -2147482644} Added
  Id: -2147482644 PK Temporary
  Name: '.NET Blog'
  Posts: [{Id: -2147482637}, {Id: -2147482636}]
Post {Id: -2147482637} Added
  Id: -2147482637 PK Temporary
  BlogId: -2147482644 FK Temporary
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: -2147482644}
Post {Id: -2147482636} Added
  Id: -2147482636 PK Temporary
  BlogId: -2147482644 FK Temporary
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: -2147482644}
```

請注意，在此案例中，已為每個實體產生 [暫存](xref:core/change-tracking/miscellaneous#temporary-values) 索引鍵值。 EF Core 會使用這些值，直到呼叫 SaveChanges 為止，此時會從資料庫中讀取實際的索引鍵值。 例如，在使用 SQLite 時：

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='.NET Blog' (Size = 9)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Blogs" ("Name")
VALUES (@p0);
SELECT "Id"
FROM "Blogs"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();

-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p2='Announcing the release of EF Core 5.0, a full featured cross-platform...' (Size = 72), @p3='Announcing the Release of EF Core 5.0' (Size = 37)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("BlogId", "Content", "Title")
VALUES (@p1, @p2, @p3);
SELECT "Id"
FROM "Posts"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();

-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String), @p1='F# 5 is the latest version of F#, the functional programming language...' (Size = 72), @p2='Announcing F# 5' (Size = 15)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("BlogId", "Content", "Title")
VALUES (@p0, @p1, @p2);
SELECT "Id"
FROM "Posts"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

完成 SaveChanges 之後，所有實體都會以其真正的索引鍵值進行更新，並在狀態中追蹤， `Unchanged` 因為它們現在符合資料庫中的狀態：

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

這與上一個使用明確索引鍵值的範例完全相同。

> [!TIP]
> 即使使用產生的索引鍵值，仍然可以設定明確的索引鍵值。 EF Core 接著會嘗試使用此索引鍵值來插入。 某些資料庫設定（包括具有識別資料行的 SQL Server）不支援這類插入，且將會擲回。

## <a name="attaching-existing-entities"></a>附加現有的實體

### <a name="explicit-key-values"></a>明確的索引鍵值

從查詢傳回的實體會在狀態中追蹤 `Unchanged` 。 `Unchanged`狀態表示實體自從查詢之後尚未經過修改。 已中斷連線的實體（可能是從 HTTP 要求中的 web 用戶端傳回）可以使用 <xref:Microsoft.EntityFrameworkCore.DbContext.Attach%2A?displayProperty=nameWithType> 、 <xref:Microsoft.EntityFrameworkCore.DbContext.AttachRange%2A?displayProperty=nameWithType> 或中的對等方法，進入此狀態 <xref:Microsoft.EntityFrameworkCore.DbSet%601> 。 例如，若要開始追蹤現有的 blog：

<!--
            context.Attach(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                });
-->
[!code-csharp[Attaching_existing_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Attaching_existing_entities_1)]

> [!NOTE]
> 這裡的範例是為了簡單起見，明確地建立實體 `new` 。 一般來說，實體實例會來自另一個來源，例如從用戶端還原序列化，或從 HTTP Post 中的資料建立。

檢查此呼叫之後的 [變更追蹤器調試](xref:core/change-tracking/debug-views) 程式，會顯示在狀態中追蹤的實體 `Unchanged` ：

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: []
```

就像 `Add` ， `Attach` 實際上會將連接實體的整個圖形設定為 `Unchanged` 狀態。 例如，若要附加現有的 blog 和相關聯的現有文章：

<!--
            context.Attach(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Id = 1,
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Id = 2,
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        }
                    }
                });
-->
[!code-csharp[Attaching_existing_entities_2](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Attaching_existing_entities_2)]

內容現在會將這些實體追蹤為 `Unchanged` ：

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

此時呼叫 SaveChanges 將沒有任何作用。 所有實體都會標示為 `Unchanged` ，因此資料庫中不會更新任何專案。

### <a name="generated-key-values"></a>產生的索引鍵值

如上所述，根據預設，會將整數和 GUID 索引 [鍵屬性](xref:core/modeling/keys) 設定為使用 [自動產生](xref:core/modeling/generated-properties) 的索引鍵值。 使用已中斷連線的實體時，這有一個主要優點：未設定的索引鍵值表示實體尚未插入資料庫。 這可讓變更追蹤器自動偵測新的實體，並將它們放入 `Added` 狀態。 例如，請考慮附加這份有關 blog 和 post 的圖表：

```c#
            context.Attach(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Id = 1,
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Id = 2,
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        },
                        new Post
                        {
                            Title = "Announcing .NET 5.0",
                            Content = ".NET 5.0 includes many enhancements, including single file applications, more..."
                        },
                    }
                });
```

<!--
            context.Attach(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Id = 1,
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Id = 2,
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        },
                        new Post
                        {
                            Title = "Announcing .NET 5.0",
                            Content = ".NET 5.0 includes many enhancements, including single file applications, more..."
                        },
                    }
                });
-->
[!code-csharp[Attaching_existing_entities_3](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Attaching_existing_entities_3)]

此 blog 的索引鍵值為1，表示它已經存在於資料庫中。 這兩篇文章也會設定索引鍵值，但第三篇則否。 EF Core 將會看到此索引鍵值為0，這是一個整數的 CLR 預設值。 這會導致 EF Core 將新實體標示為， `Added` 而不是 `Unchanged` ：

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}, {Id: -2147482636}]
Post {Id: -2147482636} Added
  Id: -2147482636 PK Temporary
  BlogId: 1 FK
  Content: '.NET 5.0 includes many enhancements, including single file a...'
  Title: 'Announcing .NET 5.0'
  Blog: {Id: 1}
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
```

此時呼叫 SaveChanges 並不會對實體執行任何動作 `Unchanged` ，但是會將新的實體插入資料庫中。 例如，在使用 SQLite 時：

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String), @p1='.NET 5.0 includes many enhancements, including single file applications, more...' (Size = 80), @p2='Announcing .NET 5.0' (Size = 19)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("BlogId", "Content", "Title")
VALUES (@p0, @p1, @p2);
SELECT "Id"
FROM "Posts"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

這裡要注意的重點是，使用產生的索引鍵值時，EF Core 可以 **自動區別已中斷連線圖形中現有實體的新** 專案。 總而言之，使用產生的索引鍵時，EF Core 一律會在實體未設定索引鍵值時插入實體。

## <a name="updating-existing-entities"></a>更新現有實體

### <a name="explicit-key-values"></a>明確的索引鍵值

<xref:Microsoft.EntityFrameworkCore.DbContext.Update%2A?displayProperty=nameWithType>、 <xref:Microsoft.EntityFrameworkCore.DbContext.UpdateRange%2A?displayProperty=nameWithType> 和等方法的 <xref:Microsoft.EntityFrameworkCore.DbSet%601> 行為與 `Attach` 上面所述的方法完全相同，不同之處在于實體會放入 `Modfied` 而非 `Unchanged` 狀態中。 例如，若要開始將現有的 blog 追蹤為 `Modified` ：

<!--
            context.Update(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                });
-->
[!code-csharp[Updating_existing_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Updating_existing_entities_1)]

檢查此呼叫之後的 [變更追蹤器調試](xref:core/change-tracking/debug-views) 程式，會顯示內容正在追蹤此實體的 `Modified` 狀態：

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog' Modified
  Posts: []
```

如同 `Add` 和 `Attach` ，實際上會 `Update` 將相關實體的 _整個圖形_ 標示為 `Modified` 。 例如，若要將現有的 blog 和相關聯的現有文章附加為 `Modified` ：

<!--
            context.Update(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Id = 1,
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Id = 2,
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        }
                    }
                });
-->
[!code-csharp[Updating_existing_entities_2](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Updating_existing_entities_2)]

內容現在會將這些實體追蹤為 `Modified` ：

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog' Modified
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Modified
  Id: 1 PK
  BlogId: 1 FK Modified Originally <null>
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...' Modified
  Title: 'Announcing the Release of EF Core 5.0' Modified
  Blog: {Id: 1}
Post {Id: 2} Modified
  Id: 2 PK
  BlogId: 1 FK Modified Originally <null>
  Content: 'F# 5 is the latest version of F#, the functional programming...' Modified
  Title: 'Announcing F# 5' Modified
  Blog: {Id: 1}
```

此時呼叫 SaveChanges 會導致所有這些實體的更新傳送至資料庫。 例如，在使用 SQLite 時：

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p0='.NET Blog' (Size = 9)], CommandType='Text', CommandTimeout='30']
UPDATE "Blogs" SET "Name" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p3='1' (DbType = String), @p0='1' (DbType = String), @p1='Announcing the release of EF Core 5.0, a full featured cross-platform...' (Size = 72), @p2='Announcing the Release of EF Core 5.0' (Size = 37)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0, "Content" = @p1, "Title" = @p2
WHERE "Id" = @p3;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p3='2' (DbType = String), @p0='1' (DbType = String), @p1='F# 5 is the latest version of F#, the functional programming language...' (Size = 72), @p2='Announcing F# 5' (Size = 15)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0, "Content" = @p1, "Title" = @p2
WHERE "Id" = @p3;
SELECT changes();
```

### <a name="generated-key-values"></a>產生的索引鍵值

使用時 `Attach` ，產生的索引鍵值與的主要優點相同：未設定的索引鍵值 `Update` 表示實體是新的，而且尚未插入資料庫。 同樣地 `Attach` ，這可讓 DbCoNtext 自動偵測新的實體，並將它們放入 `Added` 狀態。 例如，請考慮 `Update` 使用此 blog 和文章的圖表來呼叫：

<!--
            context.Update(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Id = 1,
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Id = 2,
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        }
                    }
                });
-->
[!code-csharp[Updating_existing_entities_3](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Updating_existing_entities_3)]

就像 `Attach` 範例一樣，會偵測到沒有金鑰值的 post，並將其設定為 `Added` 狀態。 其他實體會標示為 `Modified` ：

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog' Modified
  Posts: [{Id: 1}, {Id: 2}, {Id: -2147482633}]
Post {Id: -2147482633} Added
  Id: -2147482633 PK Temporary
  BlogId: 1 FK
  Content: '.NET 5.0 includes many enhancements, including single file a...'
  Title: 'Announcing .NET 5.0'
  Blog: {Id: 1}
Post {Id: 1} Modified
  Id: 1 PK
  BlogId: 1 FK Modified Originally <null>
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...' Modified
  Title: 'Announcing the Release of EF Core 5.0' Modified
  Blog: {Id: 1}
Post {Id: 2} Modified
  Id: 2 PK
  BlogId: 1 FK Modified Originally <null>
  Content: 'F# 5 is the latest version of F#, the functional programming...' Modified
  Title: 'Announcing F# 5' Modified
  Blog: {Id: 1}
```

此時呼叫 `SaveChanges` 會導致將更新傳送至資料庫，以供所有現有的實體，同時插入新的實體。 例如，在使用 SQLite 時：

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p0='.NET Blog' (Size = 9)], CommandType='Text', CommandTimeout='30']
UPDATE "Blogs" SET "Name" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p3='1' (DbType = String), @p0='1' (DbType = String), @p1='Announcing the release of EF Core 5.0, a full featured cross-platform...' (Size = 72), @p2='Announcing the Release of EF Core 5.0' (Size = 37)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0, "Content" = @p1, "Title" = @p2
WHERE "Id" = @p3;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p3='2' (DbType = String), @p0='1' (DbType = String), @p1='F# 5 is the latest version of F#, the functional programming language...' (Size = 72), @p2='Announcing F# 5' (Size = 15)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0, "Content" = @p1, "Title" = @p2
WHERE "Id" = @p3;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String), @p1='.NET 5.0 includes many enhancements, including single file applications, more...' (Size = 80), @p2='Announcing .NET 5.0' (Size = 19)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("BlogId", "Content", "Title")
VALUES (@p0, @p1, @p2);
SELECT "Id"
FROM "Posts"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

這是從中斷連線的圖形產生更新和插入的簡易方式。 不過，它會導致每個所追蹤實體的每個屬性都會將更新或插入傳送至資料庫，即使某些屬性值可能尚未變更也是一樣。 不太害怕，對於具有小型圖形的許多應用程式而言，這可能是產生更新的簡單、實用方式。 話雖如此，其他更複雜的模式有時可能會產生更有效率的更新，如 [EF Core 的身分識別解析](xref:core/change-tracking/identity-resolution)中所述。

## <a name="deleting-existing-entities"></a>刪除現有實體

對於要由 SaveChanges 刪除的實體，必須在狀態中追蹤 `Deleted` 。 實體通常會藉 `Deleted` 由呼叫的其中一個 <xref:Microsoft.EntityFrameworkCore.DbContext.Remove%2A?displayProperty=nameWithType> 、或對 <xref:Microsoft.EntityFrameworkCore.DbContext.RemoveRange%2A?displayProperty=nameWithType> 等的方法來置於狀態 <xref:Microsoft.EntityFrameworkCore.DbSet%601> 。 例如，若要將現有的文章標示為 `Deleted` ：

<!--
            context.Remove(
                new Post
                {
                    Id = 2
                });
-->
[!code-csharp[Deleting_existing_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Deleting_existing_entities_1)]

檢查此呼叫之後的 [變更追蹤器調試](xref:core/change-tracking/debug-views) 程式，會顯示內容正在追蹤狀態中的實體 `Deleted` ：

```output
Post {Id: 2} Deleted
  Id: 2 PK
  BlogId: <null> FK
  Content: <null>
  Title: <null>
  Blog: <null>
```

呼叫 SaveChanges 時，將會刪除此實體。 例如，在使用 SQLite 時：

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();
```

完成 SaveChanges 之後，已刪除的實體會從 DbCoNtext 卸離，因為它已不存在於資料庫中。 因為沒有正在追蹤的實體，所以 debug view 是空的。

## <a name="deleting-dependentchild-entities"></a>刪除相依/子系實體

從圖形刪除相依/子系實體，比刪除主體/父實體更簡單。 如需詳細資訊，請參閱下一節及 [變更外鍵和](xref:core/change-tracking/relationship-changes) 導覽。

`Remove`在以建立的實體上呼叫是不尋常的 `new` 。 此外，與和不同的是，在 `Add` `Attach` `Update` `Remove` 或狀態中尚未追蹤的實體上呼叫是很罕見的 `Unchanged` `Modified` 。 相反地，通常會追蹤相關實體的單一實體或圖形，然後呼叫 `Remove` 應該刪除的實體。 追蹤實體的這個圖形通常是透過下列其中一種方式來建立：

1. 執行實體的查詢
2. 在已 `Attach` `Update` 中斷連線的實體圖形上使用或方法，如先前章節所述。

例如，上一節中的程式碼比較可能從用戶端取得 post，然後執行如下的動作：

<!--
            context.Attach(post);
            context.Remove(post);
-->
[!code-csharp[Deleting_dependent_child_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Deleting_dependent_child_entities_1)]

這與上一個範例的行為完全相同，因為 `Remove` 在取消追蹤的實體上呼叫會導致它先附加，然後標記為 `Deleted` 。

在更實際的範例中，會先附加實體的圖表，然後將某些實體標示為已刪除。 例如：

<!--
            // Attach a blog and associated posts
            context.Attach(blog);

            // Mark one post as Deleted
            context.Remove(blog.Posts[1]);
-->
[!code-csharp[Deleting_dependent_child_entities_2](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Deleting_dependent_child_entities_2)]

所有實體都會標示為 `Unchanged` ，但呼叫的實體除外 `Remove` ：

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Deleted
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

呼叫 SaveChanges 時，將會刪除此實體。 例如，在使用 SQLite 時：

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();
```

完成 SaveChanges 之後，已刪除的實體會從 DbCoNtext 卸離，因為它已不存在於資料庫中。 其他實體仍處於 `Unchanged` 狀態：

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
```

## <a name="deleting-principalparent-entities"></a>刪除主體/父實體

連接兩個實體類型的每個關聯性都有一個主體或父端，以及一個相依或子系。 相依/子系實體是具有外鍵屬性的實體。 在一對多關聯性中，主體/父系是在「一」端，而相依/子系位於「多」端。 如需詳細資訊，請參閱 [關聯](xref:core/modeling/relationships) 性。

在上述範例中，我們會刪除貼文，也就是 blog 文章一對多關聯性中的相依/子系實體。 這相當簡單，因為移除相依/子系實體並不會影響其他實體。 另一方面，刪除主體/父實體也必須影響任何相依/子系實體。 若未這麼做，則會保留外鍵值，參考已不存在的主鍵值。 這是不正確模型狀態，而且在大部分資料庫中都會產生參考條件約束錯誤。

您可以透過兩種方式來處理這個不正確模型狀態：

1. 將 FK 值設定為 null。 這表示相依項/子系不再與任何主體/父系相關。 這是選擇性關聯性的預設值，其中外鍵必須是可為 null。 將 FK 設定為 null 對必要的關聯性而言是不正確，因為外鍵通常不能為 null。
2. 正在刪除相依項/子系。 這是必要關聯性的預設值，也適用于選擇性的關聯性。

如需變更追蹤和關聯性的詳細資訊，請參閱變更 [外鍵和](xref:core/change-tracking/relationship-changes) 導覽。

### <a name="optional-relationships"></a>組織關係

在 `Post.BlogId` 我們使用的模型中，外鍵屬性可為 null。 這表示關聯性是選擇性的，因此 EF Core 的預設行為是在 `BlogId` 刪除 blog 時將外鍵屬性設定為 null。 例如：

<!--
            // Attach a blog and associated posts
            context.Attach(blog);

            // Mark one post as Deleted
            context.Remove(blog.Posts[1]);
-->
[!code-csharp[Deleting_principal_parent_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Deleting_principal_parent_entities_1)]

在呼叫後檢查 [變更追蹤](xref:core/change-tracking/debug-views) 器的偵錯工具，就 `Remove` 會如預期般將 blog 標示為 `Deleted` ：

```output
Blog {Id: 1} Deleted
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Modified
  Id: 1 PK
  BlogId: <null> FK Modified Originally 1
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: <null>
Post {Id: 2} Modified
  Id: 2 PK
  BlogId: <null> FK Modified Originally 1
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: <null>
```

更有趣的是，所有相關貼文現在都會標示為 `Modified` 。 這是因為每個實體中的外鍵屬性都已設為 null。 呼叫 SaveChanges 會先在資料庫中，將每個 post 的外鍵值更新為 null，然後再刪除 blog：

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p0=NULL], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p1='2' (DbType = String), @p0=NULL], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p2='1' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Blogs"
WHERE "Id" = @p2;
SELECT changes();
```

完成 SaveChanges 之後，已刪除的實體會從 DbCoNtext 卸離，因為它已不存在於資料庫中。 其他實體現在會標示為 `Unchanged` 具有 null 外鍵值，這些值符合資料庫的狀態：

```output
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: <null> FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: <null>
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: <null> FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: <null>
```

### <a name="required-relationships"></a>必要關係

如果 `Post.BlogId` 外鍵屬性不可為 null，則 blog 和 post 之間的關聯性會變成「必要」。 在這種情況下，EF Core 預設會在刪除主體/父系時，刪除相依/子系實體。 例如，刪除包含相關文章的 blog，如先前的範例所示：

<!--
            // Attach a blog and associated posts
            context.Attach(blog);

            // Mark one post as Deleted
            context.Remove(blog.Posts[1]);
-->
[!code-csharp[Deleting_principal_parent_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysRequiredSamples.cs?name=Deleting_principal_parent_entities_1)]

在呼叫後檢查 [變更追蹤](xref:core/change-tracking/debug-views) 器的偵錯工具，就 `Remove` 會如預期般將 blog 標示為 `Deleted` ：

```output
Blog {Id: 1} Deleted
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Deleted
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Deleted
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

在此情況下，更有趣的是，所有相關文章也都標示為 `Deleted` 。 呼叫 SaveChanges 會導致從資料庫中刪除 blog 和所有相關文章：

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Blogs"
WHERE "Id" = @p1;
```

完成 SaveChanges 之後，所有刪除的實體都會從 DbCoNtext 卸離，因為它們不再存在於資料庫中。 因此，偵錯工具的輸出會是空的。

> [!NOTE]
> 這份檔只會在 EF Core 中使用關聯性的表面上有劃痕。 如需有關模型關聯性的詳細資訊，[以及如何](xref:core/change-tracking/relationship-changes)在呼叫 SaveChanges 時更新/刪除相依/子系實體的詳細資訊，請參閱[關聯](xref:core/modeling/relationships)性。

## <a name="custom-tracking-with-trackgraph"></a>使用 TrackGraph 的自訂追蹤

<xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.TrackGraph%2A?displayProperty=nameWithType> 的運作方式類似 `Add` ，但 `Attach` `Update` 它會在追蹤之前為每個實體實例產生回呼。 這可讓您在決定如何追蹤圖形中的個別實體時使用自訂邏輯。

例如，請考慮在使用產生的索引鍵值追蹤實體時，EF Core 所使用的規則：如果 kye 值為零，則實體是新的，而且應該插入。 讓我們擴充此規則，以找出索引鍵值是否為負數，則應該刪除實體。 這可讓我們變更已中斷連線圖形之實體中的主鍵值，以標記已刪除的實體：

<!--
            blog.Posts.Add(
                new Post
                {
                    Title = "Announcing .NET 5.0",
                    Content = ".NET 5.0 includes many enhancements, including single file applications, more..."
                }
            );

            var toDelete = blog.Posts.Single(e => e.Title == "Announcing F# 5");
            toDelete.Id = -toDelete.Id;
-->
[!code-csharp[Custom_tracking_with_TrackGraph_1a](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Custom_tracking_with_TrackGraph_1a)]

然後，您可以使用 TrackGraph 來追蹤這個已中斷連線的圖形：

<!--
        public static void UpdateBlog(Blog blog)
        {
            using var context = new BlogsContext();

            context.ChangeTracker.TrackGraph(
                blog, node =>
                    {
                        var propertyEntry = node.Entry.Property("Id");
                        var keyValue = (int)propertyEntry.CurrentValue;

                        if (keyValue == 0)
                        {
                            node.Entry.State = EntityState.Added;
                        }
                        else if (keyValue < 0)
                        {
                            propertyEntry.CurrentValue = -keyValue;
                            node.Entry.State = EntityState.Deleted;
                        }
                        else
                        {
                            node.Entry.State = EntityState.Modified;
                        }

                        Console.WriteLine($"Tracking {node.Entry.Metadata.DisplayName()} with key value {keyValue} as {node.Entry.State}");

                    });

            context.SaveChanges();
        }
-->
[!code-csharp[Custom_tracking_with_TrackGraph_1b](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Custom_tracking_with_TrackGraph_1b)]

針對圖形中的每個實體，上述程式碼會在 _追蹤實體之前，先_ 檢查主鍵值。 針對取消設定 (零) 索引鍵值，程式碼會執行 EF Core 通常會執行的動作。 亦即，如果未設定索引鍵，則會將實體標示為 `Added` 。 如果已設定索引鍵且值為非負數，則會將實體標示為 `Modified` 。 但是，如果找到負的索引鍵值，則會還原其真實的非負數值，並將該實體追蹤為 `Deleted` 。

執行此程式碼的輸出為：

```output
Tracking Blog with key value 1 as Modified
Tracking Post with key value 1 as Modified
Tracking Post with key value -2 as Deleted
Tracking Post with key value 0 as Added
```

> [!NOTE]
> 為了簡單起見，此程式碼假設每個實體都有一個稱為的整數主要索引鍵屬性 `Id` 。 這可能會編寫至抽象基類或介面。 或者，您可以從中繼資料取得 primary key 屬性或屬性， <xref:Microsoft.EntityFrameworkCore.Metadata.IEntityType> 讓此程式碼能夠使用任何類型的實體。

TrackGraph 有兩個多載。 在上述使用的簡單多載中，EF Core 會決定何時停止遍歷圖形。 具體而言，它會在該實體已被追蹤或回呼未開始追蹤實體時，停止從指定的實體造訪新的相關實體。

Advanced 多載（ <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.TrackGraph%60%601(System.Object,%60%600,System.Func{Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntryGraphNode{%60%600},System.Boolean})?displayProperty=nameWithType> ）具有傳回 bool 的回呼。 如果回呼傳回 false，則會停止圖形遍歷，否則會繼續進行。 使用此多載時，必須小心避免無限迴圈。

Advanced 多載也可讓您將狀態提供給 TrackGraph，此狀態接著會傳遞至每個回呼。
