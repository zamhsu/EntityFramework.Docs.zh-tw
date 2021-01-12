---
title: 變更追蹤-EF Core
description: EF Core 的變更追蹤總覽
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/index
ms.openlocfilehash: 52223e5472b09271d19ac9449a3989b4a0e277f7
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129685"
---
# <a name="change-tracking-in-ef-core"></a>EF Core 中的變更追蹤

每個 <xref:Microsoft.EntityFrameworkCore.DbContext> 實例都會追蹤對實體所做的變更。 接著，這些追蹤的實體會在呼叫時驅動資料庫的變更 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> 。

本檔概述 Entity Framework Core 的 (EF Core) 變更追蹤，以及它與查詢和更新之間的關聯。

> [!TIP]
> 您可以 [從 GitHub 下載範例程式碼](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/ChangeTrackingInEFCore)，以執行並偵測到本檔中的所有程式碼。

> [!TIP]
> 為了簡單起見，本檔會使用和參考同步方法（例如）， <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> 而不是其非同步對應專案（例如） <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A> 。 除非另有說明，否則呼叫和等候 async 方法可以替代。

## <a name="how-to-track-entities"></a>如何追蹤實體

當實體實例為時，就會進行追蹤：

- 從針對資料庫執行的查詢傳回
- 由 `Add` 、 `Attach` 、 `Update` 或類似方法明確附加至 DbCoNtext
- 偵測到連接到現有追蹤實體的新實體

當下列情況時，不會再追蹤實體實例：

- 已處置 DbCoNtext
-  (EF Core 5.0 和更新版本中已清除變更追蹤程式) 
- 實體已明確卸離

DbCoNtext 的設計目的是代表短期的工作單位，如 [DbCoNtext 初始化和](xref:core/dbcontext-configuration/index)設定中所述。 這表示處置 DbCoNtext 是停止追蹤實體 _的正常方式_ 。 換句話說，DbCoNtext 的存留期應該是：

1. 建立 DbCoNtext 實例
2. 追蹤某些實體
3. 對實體進行一些變更
4. 呼叫 SaveChanges 以更新資料庫
5. 處置 DbCoNtext 實例

> [!TIP]
> 採用這種方法時，不需要清除變更追蹤器或明確卸離實體實例。 但是，如果您需要卸離實體，則呼叫 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Clear%2A?displayProperty=nameWithType> 會比將實體逐一卸離的效率更高。

## <a name="entity-states"></a>實體狀態

每個實體都與指定的相關聯 <xref:Microsoft.EntityFrameworkCore.EntityState> ：

- `Detached` 未追蹤實體 <xref:Microsoft.EntityFrameworkCore.DbContext> 。
- `Added` 實體是新的，而且尚未插入資料庫中。 這表示在呼叫時將會插入它們 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> 。
- `Unchanged` 實體從資料庫查詢 _以來尚未變更_ 。 從查詢傳回的所有實體一開始都會處於此狀態。
- `Modified` 實體自資料庫查詢之後已經變更。 這表示在呼叫 SaveChanges 時將會更新它們。
- `Deleted` 實體存在於資料庫中，但在呼叫 SaveChanges 時標示為已刪除。

EF Core 會追蹤屬性層級的變更。 例如，如果只修改了單一屬性值，資料庫更新將只會變更該值。 不過，只有當實體本身處於修改狀態時，才能將屬性標示為已修改。  (或從替代的觀點來看，修改的狀態表示至少有一個屬性值標示為已修改。 ) 

下表摘要說明不同的狀態：

| 實體狀態     | DbCoNtext 追蹤 | 存在於資料庫中 | 修改的屬性 | SaveChanges 上的動作
|:-----------------|----------------------|--------------------|---------------------|-----------------------
| `Detached`       | 否                   | -                  | -                   | -
| `Added`          | 是                  | 否                 | -                   | 插入
| `Unchanged`      | 是                  | 是                | 否                  | -
| `Modified`       | 是                  | 是                | 是                 | 更新
| `Deleted`        | 是                  | 是                | -                   | 刪除

> [!NOTE]
> 為了清楚起見，此文字使用關係資料庫詞彙。 NoSQL 資料庫通常會支援類似的作業，但可能會有不同的名稱。 如需詳細資訊，請參閱您的資料庫提供者檔。

## <a name="tracking-from-queries"></a>從查詢追蹤

當相同的 <xref:Microsoft.EntityFrameworkCore.DbContext> 實例用於查詢實體，並藉由呼叫來更新時，EF Core 變更追蹤的效果最佳。 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> 這是因為 EF Core 會自動追蹤查詢實體的狀態，然後在呼叫 SaveChanges 時，偵測對這些實體所做的任何變更。

這種方法有幾個優點，而不是 [明確追蹤實體實例](xref:core/change-tracking/explicit-tracking)：

- 它為簡單式。 實體狀態很少需要明確操作，EF Core 負責處理狀態變更。
- 更新僅限於實際變更的值。
- 系統會保留 [陰影屬性](xref:core/modeling/shadow-properties) 的值，並視需要加以使用。 當外鍵儲存在陰影狀態時，這會特別相關。
- 系統會自動保留屬性的原始值，並將其用於有效率的更新。

## <a name="simple-query-and-update"></a>簡單查詢和更新

例如，假設有一個簡單的 blog/post 模型：

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

我們可以使用此模型來查詢 blog 和文章，然後對資料庫進行一些更新：

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

            blog.Name = ".NET Blog (Updated!)";

            foreach (var post in blog.Posts.Where(e => !e.Title.Contains("5.0")))
            {
                post.Title = post.Title.Replace("5", "5.0");
            }

            context.SaveChanges();
-->
[!code-csharp[Simple_query_and_update_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Simple_query_and_update_1)]

呼叫 SaveChanges 會導致下列資料庫更新，並使用 SQLite 作為範例資料庫：

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p0='.NET Blog (Updated!)' (Size = 20)], CommandType='Text', CommandTimeout='30']
UPDATE "Blogs" SET "Name" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p1='2' (DbType = String), @p0='Announcing F# 5.0' (Size = 17)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "Title" = @p0
WHERE "Id" = @p1;
SELECT changes();
```

[變更追蹤](xref:core/change-tracking/debug-views)器的偵錯工具可讓您以視覺化方式呈現正在追蹤的實體及其狀態。 例如，在呼叫 SaveChanges 之前，將下列程式碼插入至上述範例：

<!--
                context.ChangeTracker.DetectChanges();
                Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Simple_query_and_update_2](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Simple_query_and_update_2)]

產生下列輸出：

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog (Updated!)' Modified Originally '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}, {Id: 3}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Modified
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5.0' Modified Originally 'Announcing F# 5'
  Blog: {Id: 1}
```

請特別注意：

- `Blog.Name`屬性會標示為修改的 (`Name: '.NET Blog (Updated!)' Modified Originally '.NET Blog'`) ，而這會導致 blog 處於 `Modified` 狀態。
- `Post.Title`Post 2 的屬性會標示為修改的 (`Title: 'Announcing F# 5.0' Modified Originally 'Announcing F# 5'`) ，這會導致此文章處於 `Modified` 狀態。
- 第2篇的其他屬性值並未變更，因此未標示為已修改。 這就是為什麼這些值不會包含在資料庫更新中的原因。
- 另一篇文章未以任何方式修改。 這就是為什麼它仍處於 `Unchanged` 狀態，而且不包含在資料庫更新中。

## <a name="query-then-insert-update-and-delete"></a>查詢、插入、更新和刪除

如同上述範例中的更新，可以與相同工作單位中的插入和刪除結合。 例如：

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

            // Modify property values
            blog.Name = ".NET Blog (Updated!)";

            // Insert a new Post
            blog.Posts.Add(new Post
            {
                Title = "What’s next for System.Text.Json?",
                Content = ".NET 5.0 was released recently and has come with many..."
            });

            // Mark an existing Post as Deleted
            var postToDelete = blog.Posts.Single(e => e.Title == "Announcing F# 5");
            context.Remove(postToDelete);

            context.ChangeTracker.DetectChanges();
            Console.WriteLine(context.ChangeTracker.DebugView.LongView);

            context.SaveChanges();
-->
[!code-csharp[Query_then_insert_update_and_delete_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Query_then_insert_update_and_delete_1)]

在此範例中：

- 從資料庫查詢和追蹤的 blog 和相關文章
- `Blog.Name`屬性已變更
- 新貼文會新增至 blog 的現有文章集合
- 藉由呼叫來標記現有的貼文以進行刪除 <xref:Microsoft.EntityFrameworkCore.DbContext.Remove%2A?displayProperty=nameWithType>

在呼叫 SaveChanges 之前再次查看 [變更追蹤器調試](xref:core/change-tracking/debug-views) 程式，會顯示 EF Core 如何追蹤這些變更：

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog (Updated!)' Modified Originally '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}, {Id: 3}, {Id: -2147482638}]
Post {Id: -2147482638} Added
  Id: -2147482638 PK Temporary
  BlogId: 1 FK
  Content: '.NET 5.0 was released recently and has come with many...'
  Title: 'What's next for System.Text.Json?'
  Blog: {Id: 1}
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

請注意：

- 此 blog 會標示為 `Modified` 。 這會產生資料庫更新。
- Post 2 標示為 `Deleted` 。 這會產生資料庫刪除。
- 具有暫存識別碼的新貼文會與 blog 1 相關聯，並標示為 `Added` 。 這會產生資料庫插入。

這會導致下列資料庫命令 (在呼叫 SaveChanges 時使用 SQLite) ：

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p0='.NET Blog (Updated!)' (Size = 20)], CommandType='Text', CommandTimeout='30']
UPDATE "Blogs" SET "Name" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String), @p1='.NET 5.0 was released recently and has come with many...' (Size = 56), @p2='What's next for System.Text.Json?' (Size = 33)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("BlogId", "Content", "Title")
VALUES (@p0, @p1, @p2);
SELECT "Id"
FROM "Posts"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

如需有關插入和刪除實體的詳細資訊，請參閱 [明確的追蹤實體](xref:core/change-tracking/explicit-tracking) 。 如需 EF Core 如何自動偵測這類變更的詳細資訊，請參閱 [變更偵測和通知](xref:core/change-tracking/change-detection) 。

> [!TIP]
> 呼叫 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.HasChanges?displayProperty=nameWithType> 以判斷是否有任何變更會導致 SaveChanges 進行資料庫的更新。 如果 HasChanges 傳回 false，則 SaveChanges 將為無作業。
