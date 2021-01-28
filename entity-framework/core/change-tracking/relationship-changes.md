---
title: 變更外鍵和導覽 EF Core
description: 如何藉由操作外鍵和導覽來變更實體之間的關聯性
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/relationship-changes
ms.openlocfilehash: b1ebe77ed29291beeef3708b603db026c38bbbec
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98983608"
---
# <a name="changing-foreign-keys-and-navigations"></a>變更外鍵和導覽

## <a name="overview-of-foreign-keys-and-navigations"></a>外鍵和導覽的總覽

Entity Framework Core (EF Core) 模型中的關聯性，會使用外鍵 (Fk) 來表示。 FK 是由關聯性中的相依或子實體上的一或多個屬性所組成。 當相依/子系上的外鍵屬性值符合 (PK) 主體/父系上的屬性時，此相依/子系實體會與指定的主體/父實體相關聯。

外鍵是在資料庫中儲存和操作關聯性的好方法，但是在應用程式程式碼中使用多個相關實體時，並不太容易。 因此，大部分的 EF Core 模型也會將「導覽」分層至 FK 表示。 導覽表單 c #/.NET 實體實例之間的參考，這些實體實例可反映將外鍵值與主要或替代索引鍵值相符的關聯。

導覽可以在關聯性的兩端使用，或完全不使用，只留下 FK 屬性。 FK 屬性可以藉由將其設為 [陰影屬性](xref:core/modeling/shadow-properties)來隱藏。 如需模型關聯性的詳細資訊，請參閱 [關聯](xref:core/modeling/relationships) 性。

> [!TIP]
> 本檔假設您已瞭解實體狀態以及 EF Core 變更追蹤的基本概念。 如需有關這些主題的詳細資訊，請參閱 [EF Core 中的變更追蹤](xref:core/change-tracking/index) 。

> [!TIP]
> 您可以 [從 GitHub 下載範例程式碼](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/ChangingFKsAndNavigations)，以執行並偵測到本檔中的所有程式碼。

### <a name="example-model"></a>範例模型

下列模型包含四個實體類型及其之間的關聯性。 程式碼中的批註會指出哪些屬性是外鍵、主鍵和導覽。

<!--
public class Blog
{
    public int Id { get; set; } // Primary key
    public string Name { get; set; }

    public IList<Post> Posts { get; } = new List<Post>(); // Collection navigation
    public BlogAssets Assets { get; set; } // Reference navigation
}

public class BlogAssets
{
    public int Id { get; set; } // Primary key
    public byte[] Banner { get; set; }

    public int BlogId { get; set; } // Foreign key
    public Blog Blog { get; set; } // Reference navigation
}

public class Post
{
    public int Id { get; set; } // Primary key
    public string Title { get; set; }
    public string Content { get; set; }

    public int? BlogId { get; set; } // Foreign key
    public Blog Blog { get; set; } // Reference navigation

    public IList<Tag> Tags { get; } = new List<Tag>(); // Skip collection navigation
}

public class Tag
{
    public int Id { get; set; } // Primary key
    public string Text { get; set; }

    public IList<Post> Posts { get; } = new List<Post>(); // Skip collection navigation
}
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Model)]

此模型中的三個關聯性為：

- 每個 blog 都可以有多個張貼 (一對多) ：
  - `Blog` 為主體/父系。
  - `Post` 這是相依/子系。 它包含 FK 屬性 `Post.BlogId` ，其值必須符合 `Blog.Id` 相關 blog 的 PK 值。
  - `Post.Blog` 是從 post 到相關的 blog 的參考導覽。 `Post.Blog` 是的反向導覽 `Blog.Posts` 。
  - `Blog.Posts` 是從 blog 到所有相關貼文的集合導覽。 `Blog.Posts` 是的反向導覽 `Post.Blog` 。
- 每個 blog 都可以有一個資產 (一對一的) ：
  - `Blog` 為主體/父系。
  - `BlogAssets` 這是相依/子系。 它包含 FK 屬性 `BlogAssets.BlogId` ，其值必須符合 `Blog.Id` 相關 blog 的 PK 值。
  - `BlogAssets.Blog` 是從資產到相關的 blog 的參考導覽。 `BlogAssets.Blog` 是的反向導覽 `Blog.Assets` 。
  - `Blog.Assets` 是從 blog 到相關聯資產的參考導覽。 `Blog.Assets` 是的反向導覽 `BlogAssets.Blog` 。
- 每一篇文章都可以有多個標記，而且每個標籤都可以有許多 (多對多) 的貼文：
  - 多對多關聯性是一層以上的 2 1 對多關聯性。 本檔稍後會討論多對多關聯性。
  - `Post.Tags` 是從 post 到所有相關標記的集合導覽。 `Post.Tags` 是的反向導覽 `Tag.Posts` 。
  - `Tag.Posts` 是從標記到所有相關貼文的集合導覽。 `Tag.Posts` 是的反向導覽 `Post.Tags` 。

如需有關如何建立關聯性模型和設定的詳細資訊，請參閱 [關聯](xref:core/modeling/relationships) 性。

## <a name="relationship-fixup"></a>關聯性修正

EF Core 會讓導覽與外鍵值保持一致，反之亦然。 也就是說，如果外鍵值變更，使其現在參考不同的主體/父實體，則會更新導覽以反映這項變更。 同樣地，如果變更導覽，則會更新相關實體的外鍵值，以反映這項變更。 這稱為「關聯性修正」。

### <a name="fixup-by-query"></a>修復（依查詢）

當從資料庫查詢實體時，會先發生修復問題。 資料庫只有外鍵值，因此當 EF Core 從資料庫建立實體實例時，會使用外鍵值來設定參考導覽，並適當地將實體新增至集合導覽。 例如，請考慮對 blog 和其相關文章和資產的查詢：

<!--
        using var context = new BlogsContext();

        var blogs = context.Blogs
            .Include(e => e.Posts)
            .Include(e => e.Assets)
            .ToList();

        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Relationship_fixup_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Relationship_fixup_1)]

針對每個 blog，EF Core 將會先建立 `Blog` 實例。 然後，當每個貼文都從資料庫載入時，就 `Post.Blog` 會將參考導覽設定為指向相關的 blog。 同樣地，post 也會新增至 `Blog.Posts` 集合導覽。 也會發生相同的問題 `BlogAssets` ，但在此情況下，這兩個導覽都是參考。 `Blog.Assets`導覽設定為指向 [資產] 實例，而 `BlogAsserts.Blog` 導覽設定為指向 [blog] 實例。

查看此查詢之後的 [變更追蹤器調試](xref:core/change-tracking/debug-views) 程式，會顯示兩個 blog，每個都有一個資產和兩個貼文：

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: {Id: 1}
  Posts: [{Id: 1}, {Id: 2}]
Blog {Id: 2} Unchanged
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: {Id: 2}
  Posts: [{Id: 3}, {Id: 4}]
BlogAssets {Id: 1} Unchanged
  Id: 1 PK
  Banner: <null>
  BlogId: 1 FK
  Blog: {Id: 1}
BlogAssets {Id: 2} Unchanged
  Id: 2 PK
  Banner: <null>
  BlogId: 2 FK
  Blog: {Id: 2}
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: []
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
  Tags: []
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 2}
  Tags: []
Post {Id: 4} Unchanged
  Id: 4 PK
  BlogId: 2 FK
  Content: 'Examine when database queries were executed and measure how ...'
  Title: 'Database Profiling with Visual Studio'
  Blog: {Id: 2}
  Tags: []
```

[調試] 視圖會顯示索引鍵值和導覽。 導覽會使用相關實體的主鍵值來顯示。 例如， `Posts: [{Id: 1}, {Id: 2}]` 在上面的輸出中，表示 `Blog.Posts` 集合導覽會分別包含兩個與主鍵1和2相關的貼文。 同樣地，對於每個與第一個 blog 相關聯的貼文， `Blog: {Id: 1}` 這一行指出 `Post.Blog` 導覽參考了主要金鑰1的 blog。

### <a name="fixup-to-locally-tracked-entities"></a>修復本機追蹤的實體

關聯性修正程式也會發生在從追蹤查詢傳回的實體與 DbCoNtext 已追蹤的實體之間。 例如，請考慮針對 blog、文章和資產執行三個不同的查詢：

<!--
        using var context = new BlogsContext();

        var blogs = context.Blogs.ToList();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        var assets = context.Assets.ToList();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        var posts = context.Posts.ToList();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Relationship_fixup_2](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Relationship_fixup_2)]
在第一次查詢時，在第一次查詢時，只會追蹤兩個 blog：

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: <null>
  Posts: []
Blog {Id: 2} Unchanged
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: <null>
  Posts: []
```

`Blog.Assets`參考導覽為 null，而且集合導覽 `Blog.Posts` 是空的，因為內容目前未追蹤任何相關聯的實體。

在第二個查詢之後， `Blogs.Assets` 已修正參考導覽以指向新追蹤的 `BlogAsset` 實例。 同樣地， `BlogAssets.Blog` 參考導覽也會設定為指向適當的已追蹤 `Blog` 實例。

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: {Id: 1}
  Posts: []
Blog {Id: 2} Unchanged
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: {Id: 2}
  Posts: []
BlogAssets {Id: 1} Unchanged
  Id: 1 PK
  Banner: <null>
  BlogId: 1 FK
  Blog: {Id: 1}
BlogAssets {Id: 2} Unchanged
  Id: 2 PK
  Banner: <null>
  BlogId: 2 FK
  Blog: {Id: 2}
```

最後，在第三個查詢之後， `Blog.Posts` 集合導覽現在會包含所有相關的貼文，而這些 `Post.Blog` 參考會指向適當的 `Blog` 實例：

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: {Id: 1}
  Posts: [{Id: 1}, {Id: 2}]
Blog {Id: 2} Unchanged
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: {Id: 2}
  Posts: [{Id: 3}, {Id: 4}]
BlogAssets {Id: 1} Unchanged
  Id: 1 PK
  Banner: <null>
  BlogId: 1 FK
  Blog: {Id: 1}
BlogAssets {Id: 2} Unchanged
  Id: 2 PK
  Banner: <null>
  BlogId: 2 FK
  Blog: {Id: 2}
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: []
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
  Tags: []
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 2}
  Tags: []
Post {Id: 4} Unchanged
  Id: 4 PK
  BlogId: 2 FK
  Content: 'Examine when database queries were executed and measure how ...'
  Title: 'Database Profiling with Visual Studio'
  Blog: {Id: 2}
  Tags: []
```

這與原始的單一查詢相同，因為已在追蹤實體的情況下 EF Core 修正導覽，即使來自多個不同的查詢也一樣。

> [!NOTE]
> 修復永遠不會導致從資料庫傳回更多資料。 它只會連接查詢所傳回或已由 DbCoNtext 追蹤的實體。 如需在序列化實體時處理重複項的詳細資訊，請參閱 [EF Core 中的識別解析](xref:core/change-tracking/identity-resolution) 。

## <a name="changing-relationships-using-navigations"></a>使用導覽變更關聯性

若要變更兩個實體之間的關聯性，最簡單的方式是藉由操作導覽，並讓 EF Core 適當地修復反向導覽和 FK 值。 可以下列方式進行：

- 新增或移除集合導覽中的實體。
- 變更參考導覽以指向不同的實體，或將它設定為 null。

### <a name="adding-or-removing-from-collection-navigations"></a>從集合導覽新增或移除

例如，讓我們將 Visual Studio blog 的其中一篇文章移至 .NET blog。 這需要先載入 blog 和貼文，然後再從一個 blog 上的導覽集合將 post 移至其他 blog 上的導覽集合：

<!--
        using var context = new BlogsContext();

        var dotNetBlog = context.Blogs.Include(e => e.Posts).Single(e => e.Name == ".NET Blog");
        var vsBlog = context.Blogs.Include(e => e.Posts).Single(e => e.Name == "Visual Studio Blog");

        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        vsBlog.Posts.Remove(post);
        dotNetBlog.Posts.Add(post);

        context.ChangeTracker.DetectChanges();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        context.SaveChanges();
-->
[!code-csharp[Changing_relationships_using_navigations_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Changing_relationships_using_navigations_1)]

> [!TIP]
> <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType>這裡需要呼叫，因為存取偵錯工具並不會[自動偵測變更](xref:core/change-tracking/change-detection)。

這是在執行上述程式碼之後列印的 debug view：

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: <null>
  Posts: [{Id: 1}, {Id: 2}, {Id: 3}]
Blog {Id: 2} Unchanged
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: <null>
  Posts: [{Id: 4}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: []
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
  Tags: []
Post {Id: 3} Modified
  Id: 3 PK
  BlogId: 1 FK Modified Originally 2
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 1}
  Tags: []
Post {Id: 4} Unchanged
  Id: 4 PK
  BlogId: 2 FK
  Content: 'Examine when database queries were executed and measure how ...'
  Title: 'Database Profiling with Visual Studio'
  Blog: {Id: 2}
  Tags: []
```

`Blog.Posts`.Net Blog 上的導覽現在有三個貼文 (`Posts: [{Id: 1}, {Id: 2}, {Id: 3}]`) 。 同樣地， `Blog.Posts` Visual Studio blog 上的導覽只會有一個 () 的貼文 `Posts: [{Id: 4}]` 。 這是預期的，因為程式碼會明確變更這些集合。

更有趣的是，即使程式碼未明確變更 `Post.Blog` 流覽，還是已修正為指向 Visual Studio 的 blog (`Blog: {Id: 1}`) 。 此外， `Post.BlogId` 外鍵值也已更新，以符合 .net blog 的主鍵值。 這項變更會變更中的 FK 值，然後在呼叫 SaveChanges 時保存到資料庫中：

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='3' (DbType = String), @p0='1' (Nullable = true) (DbType = String)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0
WHERE "Id" = @p1;
SELECT changes();
```

### <a name="changing-reference-navigations"></a>變更參考導覽

在先前的範例中，會在每個 blog 上操作貼文的集合流覽，以將貼文從某個 blog 移至另一個。 您可以改為將 `Post.Blog` 參考導覽變更為指向新的 blog，以達成相同的目的。 例如：

<!--
        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        post.Blog = dotNetBlog;
-->
[!code-csharp[Changing_relationships_using_navigations_2](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Changing_relationships_using_navigations_2)]

這項變更之後的偵錯工具看起來與之前的範例 _完全相同_ 。 這是因為 EF Core 偵測到參考導覽變更，然後將集合導覽和 FK 值修正為相符。

## <a name="changing-relationships-using-foreign-key-values"></a>使用外鍵值變更關聯性

在上一節中，關聯性是由導航操作，讓外鍵值自動更新。 這是在 EF Core 中操作關聯性的建議方式。 不過，您也可以直接操作 FK 值。 例如，我們可以變更外鍵值，將貼文從某個 blog 移至另一個 `Post.BlogId` ：

<!--
        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        post.BlogId = dotNetBlog.Id;
-->
[!code-csharp[Changing_relationships_using_foreign_key_values_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Changing_relationships_using_foreign_key_values_1)]

請注意，這與變更參考導覽的方式非常類似，如先前的範例所示。

這項變更之後的偵錯工具會再次與先前兩個範例的案例 _完全相同_ 。 這是因為 EF Core 偵測到 FK 值變更，然後將參考和集合導覽全部修正為相符。

> [!TIP]
> 當關聯性變更時，請勿撰寫程式碼來操作所有導覽和 FK 值。 這類程式碼更複雜，而且必須確保每個案例中的外鍵和導覽一致變更。 可能的話，只要操作單一導覽，或可能兩種導覽。 如有需要，只要操作 FK 值即可。 避免同時操作導覽和 FK 值。

## <a name="fixup-for-added-or-deleted-entities"></a>新增或刪除之實體的修復

### <a name="adding-to-a-collection-navigation"></a>加入至集合導覽

EF Core 在 [偵測](xref:core/change-tracking/change-detection) 到新的相依/子實體已加入至集合流覽時，會執行下列動作：

- 如果未追蹤實體，則會進行追蹤。  (實體通常會處於 `Added` 狀態。 但是，如果實體類型設定為使用產生的索引鍵，且已設定主鍵值，則會以狀態追蹤實體 `Unchanged` 。 ) 
- 如果實體與不同的主體/父系相關聯，則會切斷該關聯性。
- 實體會與擁有集合導覽的主體/父系產生關聯。
- 所有相關實體都會修正導覽和外鍵值。

根據這一點，我們可以看到，將貼文從某個 blog 移至另一個，其實不需要將它從舊的集合導覽中移除，再將它新增至新的。 因此，您可以從上述範例中的程式碼進行變更：

<!--
        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        vsBlog.Posts.Remove(post);
        dotNetBlog.Posts.Add(post);
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_1)]

變更為：

<!--
        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        dotNetBlog.Posts.Add(post);
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_2](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_2)]

EF Core 看到貼文已新增至新的 blog，並會自動從第一個 blog 的集合中移除。

### <a name="removing-from-a-collection-navigation"></a>從集合導覽中移除

從主體/父系的集合導覽中移除相依/子系實體，會導致切斷與該主體/父系的關聯性。 接下來會發生什麼事，取決於關聯性是選擇性或必要。

#### <a name="optional-relationships"></a>組織關係

根據預設，選擇性的關聯性會將外鍵值設定為 null。 這表示相依/子系不再與 _任何_ 主體/父系相關聯。 例如，讓我們載入一個 blog 和文章，然後從集合導覽中移除其中一篇文章 `Blog.Posts` ：

<!--
        var post = dotNetBlog.Posts.Single(e => e.Title == "Announcing F# 5");
        dotNetBlog.Posts.Remove(post);
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_3](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_3)]

在此變更之後查看 [變更追蹤調試](xref:core/change-tracking/debug-views) 程式，會顯示：

- `Post.BlogId`FK 已設定為 null (`BlogId: <null> FK Modified Originally 1`) 
- `Post.Blog`參考導覽已設定為 null (`Blog: <null>`) 
- 貼文已從 `Blog.Posts` 集合導覽 (移除 `Posts: [{Id: 1}]`) 

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: <null>
  Posts: [{Id: 1}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: []
Post {Id: 2} Modified
  Id: 2 PK
  BlogId: <null> FK Modified Originally 1
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: <null>
  Tags: []
```

請注意，貼文 _未標示_ 為 `Deleted` 。 它會標示為， `Modified` 以便在呼叫 SaveChanges 時，將資料庫中的 FK 值設定為 null。

#### <a name="required-relationships"></a>必要關係

 (不允許將 FK 值設定為 null，而且通常不可能) 需要的關聯性。 因此，切斷所需的關聯性，表示相依/子系實體必須重設為新主體/父系的父代，或在呼叫 SaveChanges 以避免參考條件約束違規時，從資料庫中移除。 這就是所謂的「刪除孤立」，這是 EF Core 中的預設行為，是必要的關聯性。

例如，讓我們變更需要的 blog 和 post 之間的關聯性，然後執行與上述範例相同的程式碼：

<!--
        var post = dotNetBlog.Posts.Single(e => e.Title == "Announcing F# 5");
        dotNetBlog.Posts.Remove(post);
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_4](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/RequiredRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_4)]

在此變更之後查看 [偵錯工具] 視圖，顯示：

- 貼文已標示為 `Deleted` 在呼叫 SaveChanges 時，從資料庫中將它刪除。
- `Post.Blog`參考導覽已設定為 null (`Blog: <null>`) 。
- 已從集合導覽移除 post `Blog.Posts` (`Posts: [{Id: 1}]`) 。

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: <null>
  Posts: [{Id: 1}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: []
Post {Id: 2} Deleted
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: <null>
  Tags: []
```

請注意， `Post.BlogId` 因為必要的關聯性，會維持不變，不能設定為 null。

呼叫 SaveChanges 會導致正在刪除孤立的 post：

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();
```

#### <a name="delete-orphans-timing-and-re-parenting"></a>刪除孤立的計時和重新父代

依預設， `Deleted` 在偵測 [到](xref:core/change-tracking/change-detection)關聯性變更時，就會立即標記孤立。 不過，在實際呼叫 SaveChanges 之前，此進程可能會延遲。 這有助於避免產生已從一個主體/父系移除的實體孤立專案，但是在呼叫 SaveChanges 之前，將會以新的主體/父系重新建立父代。 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DeleteOrphansTiming?displayProperty=nameWithType> 用來設定此時間。 例如：

<!--
        context.ChangeTracker.DeleteOrphansTiming = CascadeTiming.OnSaveChanges;

        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        vsBlog.Posts.Remove(post);

        context.ChangeTracker.DetectChanges();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        dotNetBlog.Posts.Add(post);

        context.ChangeTracker.DetectChanges();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        context.SaveChanges();
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_5](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/RequiredRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_5)]

從第一個集合中移除 post 之後，物件未標示為 `Deleted` 先前範例中的。 相反地，EF Core 會追蹤關聯性是否已中斷， _即使這是必要的關聯_ 性也是一樣。  (FK 值會被 EF Core 視為 null，即使該型別不可為 null 也是一樣。 這就是所謂的「概念 null」。 ) 

```output
Post {Id: 3} Modified
  Id: 3 PK
  BlogId: <null> FK Modified Originally 2
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: <null>
  Tags: []
```

此時呼叫 SaveChanges 會導致刪除孤立的貼文。 不過，如上述範例所示，在呼叫 SaveChanges 之前，post 會與新的 blog 相關聯，然後它會適當地修正至新的 blog，而且不再被視為孤立：

```output
Post {Id: 3} Modified
  Id: 3 PK
  BlogId: 1 FK Modified Originally 2
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 1}
  Tags: []
```

此時呼叫的 SaveChanges 將會更新資料庫中的貼文，而不是刪除它。

您也可以關閉自動刪除孤立的功能。 如果在追蹤孤立的同時呼叫 SaveChanges，這將會導致例外狀況。 例如，下列程式碼：

<!--
                var dotNetBlog = context.Blogs.Include(e => e.Posts).Single(e => e.Name == ".NET Blog");

                context.ChangeTracker.DeleteOrphansTiming = CascadeTiming.Never;

                var post = dotNetBlog.Posts.Single(e => e.Title == "Announcing F# 5");
                dotNetBlog.Posts.Remove(post);

                context.SaveChanges(); // Throws
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_6](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/RequiredRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_6)]

將擲回這個例外狀況：

> InvalidOperationException：實體 ' Blog ' 和 ' Post ' 之間的關聯（具有索引鍵值 ' {BlogId： 1} '）已遭到中斷，但關聯性標示為必要或隱含需要，因為外鍵不可為 null。 如果相依/子系實體在必要的關聯性中斷時應該刪除，請將關聯性設定為使用串聯刪除。

您可以藉由呼叫來隨時強制刪除遺孤以及串聯刪除 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeChanges?displayProperty=nameWithType> 。 將此項與設定 [刪除孤立時間] 相結合，將可確保不會 `Never` 刪除孤立時間，除非明確指示 EF Core。

### <a name="changing-a-reference-navigation"></a>變更參考導覽

變更一對多關聯性的參考導覽與變更關聯性另一端的集合導覽具有相同的效果。 將相依/子系的參考導覽設定為 null，相當於從主體/父系的集合導覽中移除實體。 所有的修復和資料庫變更都會依照上一節所述進行，包括在需要關聯性的情況下將實體設為孤立。

#### <a name="optional-one-to-one-relationships"></a>選擇性的一對一關聯性

若為一對一關聯性，變更參考導覽會導致任何先前的關聯性被中斷。 對於選擇性的關聯性，這表示先前相關的相依/子系上的 FK 值設定為 null。 例如：

<!--
        using var context = new BlogsContext();

        var dotNetBlog = context.Blogs.Include(e => e.Assets).Single(e => e.Name == ".NET Blog");
        dotNetBlog.Assets = new BlogAssets();

        context.ChangeTracker.DetectChanges();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        context.SaveChanges();
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_7](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_7)]

在呼叫 SaveChanges 之前，偵錯工具會顯示新的資產已取代現有的資產，現在已標示為 `Modified` 具有 null `BlogAssets.BlogId` FK 值：

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: {Id: -2147482629}
  Posts: []
BlogAssets {Id: -2147482629} Added
  Id: -2147482629 PK Temporary
  Banner: <null>
  BlogId: 1 FK
  Blog: {Id: 1}
BlogAssets {Id: 1} Modified
  Id: 1 PK
  Banner: <null>
  BlogId: <null> FK Modified Originally 1
  Blog: <null>
```

這會導致在呼叫 SaveChanges 時產生更新和插入：

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p0=NULL], CommandType='Text', CommandTimeout='30']
UPDATE "Assets" SET "BlogId" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p2=NULL, @p3='1' (Nullable = true) (DbType = String)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Assets" ("Banner", "BlogId")
VALUES (@p2, @p3);
SELECT "Id"
FROM "Assets"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

#### <a name="required-one-to-one-relationships"></a>需要一對一關聯性

執行與上述範例相同的程式碼，但這次使用必要的一對一關聯性，顯示先前相關聯的 `BlogAssets` 已標示為 `Deleted` ，因為當新的位置為新的時，它會變成孤立的狀態 `BlogAssets` ：

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: {Id: -2147482639}
  Posts: []
BlogAssets {Id: -2147482639} Added
  Id: -2147482639 PK Temporary
  Banner: <null>
  BlogId: 1 FK
  Blog: {Id: 1}
BlogAssets {Id: 1} Deleted
  Id: 1 PK
  Banner: <null>
  BlogId: 1 FK
  Blog: <null>
```

然後，這會在呼叫 SaveChanges 時產生 delete 和 insert：

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Assets"
WHERE "Id" = @p0;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p1=NULL, @p2='1' (DbType = String)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Assets" ("Banner", "BlogId")
VALUES (@p1, @p2);
SELECT "Id"
FROM "Assets"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

將孤立的標記標記為已刪除的時間，可以像針對集合導覽所顯示的相同方式變更，而且具有相同的效果。

### <a name="deleting-an-entity"></a>刪除實體

#### <a name="optional-relationships"></a>組織關係

當實體標示為 `Deleted` （例如藉由呼叫）時 <xref:Microsoft.EntityFrameworkCore.DbContext.Remove%2A?displayProperty=nameWithType> ，已刪除之實體的參考會從其他實體的導覽中移除。 若為選擇性的關聯性，則會將相依實體中的 FK 值設定為 null。

例如，讓我們將 Visual Studio 的 blog 標示為 `Deleted` ：

<!--
        using var context = new BlogsContext();

        var vsBlog = context.Blogs
            .Include(e => e.Posts)
            .Include(e => e.Assets)
            .Single(e => e.Name == "Visual Studio Blog");

        context.Remove(vsBlog);

        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        context.SaveChanges();
-->
[!code-csharp[Deleting_an_entity_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Deleting_an_entity_1)]

在呼叫 SaveChanges 之前，查看 [變更追蹤](xref:core/change-tracking/debug-views) 器的偵錯工具顯示：

```output
Blog {Id: 2} Deleted
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: {Id: 2}
  Posts: [{Id: 3}, {Id: 4}]
BlogAssets {Id: 2} Modified
  Id: 2 PK
  Banner: <null>
  BlogId: <null> FK Modified Originally 2
  Blog: <null>
Post {Id: 3} Modified
  Id: 3 PK
  BlogId: <null> FK Modified Originally 2
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: <null>
  Tags: []
Post {Id: 4} Modified
  Id: 4 PK
  BlogId: <null> FK Modified Originally 2
  Content: 'Examine when database queries were executed and measure how ...'
  Title: 'Database Profiling with Visual Studio'
  Blog: <null>
  Tags: []
```

請注意：

- 此 blog 會標示為 `Deleted` 。
- 與已刪除的 blog 相關的資產具有 null FK 值 (`BlogId: <null> FK Modified Originally 2`) 和 null 參考流覽 (`Blog: <null>`) 
- 與已刪除的 blog 相關的每個貼文都有 null FK 值 (`BlogId: <null> FK Modified Originally 2`) 和 null 參考流覽 (`Blog: <null>`) 

#### <a name="required-relationships"></a>必要關係

必要關聯性的修復行為與選用的關聯性相同，不同之處在于相依/子系實體會標示為， `Deleted` 因為它們不能在沒有主體/父系的情況下存在，而且必須在呼叫 SaveChanges 時從資料庫中移除，以避免參考條件約束例外狀況。 這就是所謂的「串聯刪除」，而且是必要關聯性 EF Core 中的預設行為。 例如，執行與上一個範例相同的程式碼，但使用必要的關聯性會導致在呼叫 SaveChanges 之前的下列偵錯工具視圖：

```output
Blog {Id: 2} Deleted
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: {Id: 2}
  Posts: [{Id: 3}, {Id: 4}]
BlogAssets {Id: 2} Deleted
  Id: 2 PK
  Banner: <null>
  BlogId: 2 FK
  Blog: {Id: 2}
Post {Id: 3} Deleted
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 2}
  Tags: []
Post {Id: 4} Deleted
  Id: 4 PK
  BlogId: 2 FK
  Content: 'Examine when database queries were executed and measure how ...'
  Title: 'Database Profiling with Visual Studio'
  Blog: {Id: 2}
  Tags: []
```

如預期般，相依項/子系現在標示為 `Deleted` 。 不過請注意，已刪除實體上的導覽尚未 _變更。_ 這看起來似乎很奇怪，但它會藉由清除所有導覽來避免將已刪除的實體圖表完全解除切。 也就是，即使在刪除後，blog、資產和貼文仍會形成實體的圖形。 這可讓您更輕鬆地刪除實體的圖形，而不是在 EF6 中切割圖形的情況。

#### <a name="cascade-delete-timing-and-re-parenting"></a>串聯刪除計時和重新父代

依預設，當父/主體標示為時，就會立即進行串聯刪除 `Deleted` 。 這與刪除孤立的相同，如先前所述。 就像刪除孤立的一樣，在呼叫 SaveChanges 之前（甚至完全停用），您可以藉由適當設定來延遲此進程 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeDeleteTiming?displayProperty=nameWithType> 。 這與刪除孤立的方式相同，包括刪除主體/父系之後，重新父代/相依項。

您可以藉由呼叫來強制執行串聯刪除，以及刪除孤立的孤立部分 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeChanges?displayProperty=nameWithType> 。 將此項與設定串聯刪除時間結合，將可確保不會發生串聯刪除， `Never` 除非明確指示 EF Core。

> [!TIP]
> 串聯刪除和刪除孤立的緊密相關。 這兩種方式都會在中斷與所需主體/父系的關聯性時，刪除相依/子系實體。 若是串聯刪除，則會發生此切斷，是因為主體/父系本身已被刪除。 若為孤立，主體/父實體仍存在，但不再與相依/子系實體相關。

## <a name="many-to-many-relationships"></a>多對多關聯性

EF Core 中的多對多關聯性是使用聯結實體來執行。 多對多關聯性的每一端都與具有一對多關聯性的這個聯結實體相關。 在 EF Core 5.0 之前，此聯結實體必須明確定義和對應。 從 EF Core 5.0 開始，可以隱含地建立並隱藏。 不過，在這兩種情況下，基礎行為都相同。 我們將先探討這個基礎行為，以瞭解多對多關聯性的追蹤運作方式。

### <a name="how-many-to-many-relationships-work"></a>多對多關聯性的運作方式

請考慮此 EF Core 模型，此模型會使用明確定義的聯結實體類型，在貼文和標記之間建立多對多關聯性：

<!--
    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }

        public int? BlogId { get; set; }
        public Blog Blog { get; set; }

        public IList<PostTag> PostTags { get; } = new List<PostTag>(); // Collection navigation
    }

    public class Tag
    {
        public int Id { get; set; }
        public string Text { get; set; }

        public IList<PostTag> PostTags { get; } = new List<PostTag>(); // Collection navigation
    }

    public class PostTag
    {
        public int PostId { get; set; } // First part of composite PK; FK to Post
        public int TagId { get; set; } // Second part of composite PK; FK to Tag

        public Post Post { get; set; } // Reference navigation
        public Tag Tag { get; set; } // Reference navigation
    }
    -->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntitySamples.cs?name=Model)]

請注意， `PostTag` 聯結實體類型包含兩個外鍵屬性。 在此模型中，針對要與標記相關的貼文，必須有一個 PostTag 聯結實體，其中 `PostTag.PostId` 外鍵值符合 `Post.Id` 主鍵值，以及 `PostTag.TagId` 外鍵值與 `Tag.Id` 主鍵值相符的值。 例如：

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            context.Add(new PostTag { PostId = post.Id, TagId = tag.Id });

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntitySamples.cs?name=Many_to_many_relationships_1)]

在執行此程式碼之後查看 [變更追蹤器調試](xref:core/change-tracking/debug-views) 程式，會顯示 post 和標記是由新的 `PostTag` 聯結實體所關聯：

```output
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: <null>
  PostTags: [{PostId: 3, TagId: 1}]
PostTag {PostId: 3, TagId: 1} Added
  PostId: 3 PK FK
  TagId: 1 PK FK
  Post: {Id: 3}
  Tag: {Id: 1}
Tag {Id: 1} Unchanged
  Id: 1 PK
  Text: '.NET'
  PostTags: [{PostId: 3, TagId: 1}]
```

請注意，和上的集合導覽已 `Post` `Tag` 修正，如同上的參考導覽 `PostTag` 。 這些關聯性可以由導覽操作，而不是 FK 值，就如同上述範例中所述。 例如，您可以藉由在聯結實體上設定參考導覽來修改上面的程式碼，以加入關聯性：

<!--
            context.Add(new PostTag { Post = post, Tag = tag });
-->
[!code-csharp[Many_to_many_relationships_2](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntitySamples.cs?name=Many_to_many_relationships_2)]

這會產生與上述範例中的 Fk 和導覽完全相同的變更。

### <a name="skip-navigations"></a>略過導覽

> [!NOTE]
> 略過導覽是在 EF Core 5.0 中引進。

手動操作聯結資料表可能很麻煩。 從 EF Core 5.0 開始，可以使用「略過」聯結實體的特殊集合導覽，直接操作多對多關聯性。 例如，您可以將兩個 skip 導覽新增至上述模型;其中一個是從 Post 至標記，另一個則是從標記到貼文：

<!--
    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }

        public int? BlogId { get; set; }
        public Blog Blog { get; set; }

        public IList<Tag> Tags { get; } = new List<Tag>(); // Skip collection navigation
        public IList<PostTag> PostTags { get; } = new List<PostTag>(); // Collection navigation
    }

    public class Tag
    {
        public int Id { get; set; }
        public string Text { get; set; }

        public IList<Post> Posts { get; } = new List<Post>(); // Skip collection navigation
        public IList<PostTag> PostTags { get; } = new List<PostTag>(); // Collection navigation
    }

    public class PostTag
    {
        public int PostId { get; set; } // First part of composite PK; FK to Post
        public int TagId { get; set; } // Second part of composite PK; FK to Tag

        public Post Post { get; set; } // Reference navigation
        public Tag Tag { get; set; } // Reference navigation
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityAndSkipsSamples.cs?name=Model)]

此多對多關聯性需要下列設定，以確保略過導覽和一般導覽全都適用于相同的多對多關聯性：

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder.Entity<Post>()
                .HasMany(p => p.Tags)
                .WithMany(p => p.Posts)
                .UsingEntity<PostTag>(
                    j => j.HasOne(t => t.Tag).WithMany(p => p.PostTags),
                    j => j.HasOne(t => t.Post).WithMany(p => p.PostTags));
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityAndSkipsSamples.cs?name=OnModelCreating)]

如需對應多對多關聯性的詳細資訊，請參閱 [關聯](xref:core/modeling/relationships) 性。

略過導覽的外觀和行為，例如一般集合導覽。 不過，它們使用外鍵值的方式不同。 讓我們將貼文與標記建立關聯，但這次使用略過導覽：

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            post.Tags.Add(tag);

            context.ChangeTracker.DetectChanges();
            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_3](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityAndSkipsSamples.cs?name=Many_to_many_relationships_3)]

請注意，此程式碼不會使用聯結實體。 它會改為將實體新增至導覽集合，其方式就像是一對多關聯性一樣。 產生的偵錯工具基本上與之前相同：

```output
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: <null>
  PostTags: [{PostId: 3, TagId: 1}]
  Tags: [{Id: 1}]
PostTag {PostId: 3, TagId: 1} Added
  PostId: 3 PK FK
  TagId: 1 PK FK
  Post: {Id: 3}
  Tag: {Id: 1}
Tag {Id: 1} Unchanged
  Id: 1 PK
  Text: '.NET'
  PostTags: [{PostId: 3, TagId: 1}]
  Posts: [{Id: 3}]
```

請注意，當 `PostTag` FK 值設定為現在相關聯之標記和貼文的 PK 值時，會自動建立聯結實體的實例。 所有一般參考和集合導覽都已修正，以符合這些 FK 值。 此外，因為此模型包含略過導覽，所以也已修正這些問題。 尤其是，即使我們將標籤新增至 [ `Post.Tags` 略過] 導覽， `Tag.Posts` 也已修正此關聯性另一端的反向略過導覽，以包含相關聯的貼文。

值得注意的是，即使在上方略過導覽，也可以直接操作基礎多對多關聯性。 例如，標記和貼文可能會與「略過導覽」簡介之前的相關聯：

<!--
            context.Add(new PostTag { Post = post, Tag = tag });
-->
[!code-csharp[Many_to_many_relationships_4](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityAndSkipsSamples.cs?name=Many_to_many_relationships_4)]

或使用 FK 值：

<!--
            context.Add(new PostTag { PostId = post.Id, TagId = tag.Id });
-->
[!code-csharp[Many_to_many_relationships_5](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityAndSkipsSamples.cs?name=Many_to_many_relationships_5)]

這仍會導致正確地修正 skip 導覽，並產生與上一個範例相同的調試視圖輸出。

### <a name="skip-navigations-only"></a>僅略過導覽

在上一節中，我們新增了 skip 導覽 _，除了_ 完整定義兩個基礎的一對多關聯性。 這有助於說明 FK 值的情況，但通常不需要。 相反地，您可以使用 [ _略過_ 導覽] 來定義多對多關聯性。 這是在此檔最上方的模型中定義多對多關聯性的方式。 您可以使用此模型，將 post 新增至 `Tag.Posts` skip 導覽 (，或將標記新增至 [ `Post.Tags` 略過] 導覽) ，藉此建立貼文和標記的關聯：

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            post.Tags.Add(tag);

            context.ChangeTracker.DetectChanges();
            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_6](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Many_to_many_relationships_6)]

進行這項變更之後，查看 [偵錯工具] 視圖會顯示 EF Core 已建立的實例 `Dictionary<string, object>` 來表示聯結實體。 此聯結實體同時包含 `PostsId` 和 `TagsId` 外鍵屬性，這些屬性已設定為符合相關聯之 post 和 TAG 的 PK 值。

```output
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: <null>
  Tags: [{Id: 1}]
Tag {Id: 1} Unchanged
  Id: 1 PK
  Text: '.NET'
  Posts: [{Id: 3}]
PostTag (Dictionary<string, object>) {PostsId: 3, TagsId: 1} Added
  PostsId: 3 PK FK
  TagsId: 1 PK FK
```

如需隱含聯結實體和實體類型使用的詳細資訊，請參閱 [關聯](xref:core/modeling/relationships) 性 `Dictionary<string, object>` 。

> [!IMPORTANT]
> 依慣例用於聯結實體類型的 CLR 型別，在未來的版本中可能會變更，以改善效能。 除非已明確設定，否則請勿依賴聯結類型 `Dictionary<string, object>` 。

### <a name="join-entities-with-payloads"></a>使用承載聯結實體

到目前為止，所有範例都使用聯結實體型別 (明確或隱含) 只包含多對多關聯性所需的兩個外鍵屬性。 在操作關聯性時，這些 FK 值都不需要由應用程式明確設定，因為它們的值來自相關實體的主要索引鍵屬性。 這可讓 EF Core 在不遺失資料的情況下建立聯結實體的實例。

#### <a name="payloads-with-generated-values"></a>具有產生值的裝載

EF Core 支援將其他屬性加入至聯結實體類型。 這就是所謂的「裝載」提供「聯結」實體。 例如，讓我們將 `TaggedOn` 屬性新增至 `PostTag` 聯結實體：

<!--
    public class PostTag
    {
        public int PostId { get; set; } // Foreign key to Post
        public int TagId { get; set; } // Foreign key to Tag

        public DateTime TaggedOn { get; set; } // Payload
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithPayloadSamples.cs?name=Model)]

當 EF Core 建立聯結實體實例時，不會設定這個承載屬性。 最常見的處理方式是使用裝載屬性搭配自動產生的值。 例如，在 `TaggedOn` 插入每個新實體時，可以將屬性設定為使用存放區產生的時間戳記：

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder.Entity<Post>()
                .HasMany(p => p.Tags)
                .WithMany(p => p.Posts)
                .UsingEntity<PostTag>(
                    j => j.HasOne<Tag>().WithMany(),
                    j => j.HasOne<Post>().WithMany(),
                    j => j.Property(e => e.TaggedOn).HasDefaultValueSql("CURRENT_TIMESTAMP"));
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithPayloadSamples.cs?name=OnModelCreating)]

您現在可以使用與之前相同的方式來標記貼文：

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            post.Tags.Add(tag);

            context.SaveChanges();

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_7](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithPayloadSamples.cs?name=Many_to_many_relationships_7)]

在呼叫 SaveChanges 之後查看 [變更追蹤器調試](xref:core/change-tracking/debug-views) 程式，會顯示已適當設定承載屬性：

```output
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: <null>
  Tags: [{Id: 1}]
PostTag {PostId: 3, TagId: 1} Unchanged
  PostId: 3 PK FK
  TagId: 1 PK FK
  TaggedOn: '12/29/2020 8:13:21 PM'
Tag {Id: 1} Unchanged
  Id: 1 PK
  Text: '.NET'
  Posts: [{Id: 3}]
```

#### <a name="explicitly-setting-payload-values"></a>明確設定承載值

在上一個範例中，讓我們新增未使用自動產生之值的承載屬性：

<!--
    public class PostTag
    {
        public int PostId { get; set; } // First part of composite PK; FK to Post
        public int TagId { get; set; } // Second part of composite PK; FK to Tag

        public DateTime TaggedOn { get; set; } // Auto-generated payload property
        public string TaggedBy { get; set; } // Not-generated payload property
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithStringPayloadSamples.cs?name=Model)]

現在可以使用與之前相同的方式來標記貼文，而聯結實體仍會自動建立。 然後可以使用 [存取追蹤實體](xref:core/change-tracking/entity-entries)中所述的其中一種機制來存取此實體。 例如，下列程式碼會使用 <xref:Microsoft.EntityFrameworkCore.DbSet%601.Find%2A?displayProperty=nameWithType> 來存取聯結實體實例：

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            post.Tags.Add(tag);

            context.ChangeTracker.DetectChanges();

            var joinEntity = context.Set<PostTag>().Find(post.Id, tag.Id);

            joinEntity.TaggedBy = "ajcvickers";

            context.SaveChanges();

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_8](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithStringPayloadSamples.cs?name=Many_to_many_relationships_8)]

一旦找到聯結實體之後，就可以用一般方式操作它，在此範例中，若要在 `TaggedBy` 呼叫 SaveChanges 之前設定承載屬性。

> [!NOTE]
> 請注意， <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> 此處需要的呼叫，讓 EF Core 有機會偵測導覽屬性變更，並在使用之前建立聯結實體實例 `Find` 。 如需詳細資訊，請參閱 [變更偵測和通知](xref:core/change-tracking/change-detection) 。

或者，您可以明確地建立聯結實體，以將貼文與標記建立關聯。 例如：

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            context.Add(new PostTag()
            {
                PostId = post.Id,
                TagId = tag.Id,
                TaggedBy = "ajcvickers"
            });

            context.SaveChanges();

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_9](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithStringPayloadSamples.cs?name=Many_to_many_relationships_9)]

最後，設定承載資料的另一種方式是 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> <xref:Microsoft.EntityFrameworkCore.DbContext.SavingChanges?displayProperty=nameWithType> 在更新資料庫之前覆寫或使用事件來處理實體。 例如：

<!--
        public override int SaveChanges()
        {
            foreach (var entityEntry in ChangeTracker.Entries<PostTag>())
            {
                if (entityEntry.State == EntityState.Added)
                {
                    entityEntry.Entity.TaggedBy = "ajcvickers";
                }
            }

            return base.SaveChanges();
        }
-->
[!code-csharp[SaveChanges](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithStringPayloadSamples.cs?name=SaveChanges)]
