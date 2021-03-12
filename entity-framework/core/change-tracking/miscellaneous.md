---
title: 其他變更追蹤功能-EF Core
description: 牽涉到 EF Core 變更追蹤的其他功能和案例
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/miscellaneous
ms.openlocfilehash: 63d96227b6862e920d900a5cc3f1f85d7c6d85ac
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/11/2021
ms.locfileid: "103024429"
---
# <a name="additional-change-tracking-features"></a>其他變更追蹤功能

本檔涵蓋涉及變更追蹤的其他功能和案例。

> [!TIP]
> 本檔假設您已瞭解實體狀態和 EF Core 變更追蹤的基本概念。 如需有關這些主題的詳細資訊，請參閱 [EF Core 中的變更追蹤](xref:core/change-tracking/index) 。

> [!TIP]
> 您可以 [從 GitHub 下載範例程式碼](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/ChangeTracking/AdditionalChangeTrackingFeatures)，以執行並偵測到本檔中的所有程式碼。

## <a name="add-versus-addasync"></a>`Add` 與 `AddAsync`

Entity Framework Core (EF Core) 提供非同步方法，只要使用該方法可能會導致資料庫互動。 也提供同步方法，以避免使用不支援高效能非同步存取的資料庫時的額外負荷。

<xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> 而且 <xref:Microsoft.EntityFrameworkCore.DbSet%601.Add%2A?displayProperty=nameWithType> 通常不會存取資料庫，因為這些方法原本就是開始追蹤實體。 不過，某些形式的值產生 _可能會_ 存取資料庫，以便產生索引鍵值。 執行這項工作並隨附于 EF Core 的唯一值產生器是 <xref:Microsoft.EntityFrameworkCore.ValueGeneration.HiLoValueGenerator%601> 。 使用這個產生器並不常見;預設不會設定它。 這表示大部分的應用程式都應該使用 `Add` ，而不是 `AddAsync` 。

和、和等其他類似的方法並 `Update` `Attach` 沒有非同步多載 `Remove` ，因為它們絕對不會產生新的索引鍵值，因此永遠不需要存取資料庫。

## <a name="addrange-updaterange-attachrange-and-removerange"></a>`AddRange`、`UpdateRange`、`AttachRange` 和 `RemoveRange`

<xref:Microsoft.EntityFrameworkCore.DbSet%601> 以及 <xref:Microsoft.EntityFrameworkCore.DbContext> 提供 `Add` `Update` `Attach` `Remove` 可在單一呼叫中接受多個實例的、、和的替代版本。 這些方法 <xref:Microsoft.EntityFrameworkCore.DbSet%601.AddRange%2A> 分別為、、 <xref:Microsoft.EntityFrameworkCore.DbSet%601.UpdateRange%2A> <xref:Microsoft.EntityFrameworkCore.DbSet%601.AttachRange%2A> 和 <xref:Microsoft.EntityFrameworkCore.DbSet%601.RemoveRange%2A> 。

提供這些方法是為了方便起見。 使用「範圍」方法的功能與對等的非範圍方法的多個呼叫相同。 這兩種方法之間並沒有顯著的效能差異。

> [!NOTE]
> 這不同于 EF6、 `AddRange` `Add` 會自動呼叫 `DetectChanges` ，但呼叫多次會導致多次呼叫 `Add` DetectChanges，而不是呼叫一次。 這 `AddRange` 在 EF6 時更有效率。 在 EF Core 中，這些方法都不會自動呼叫 `DetectChanges` 。

## <a name="dbcontext-versus-dbset-methods"></a>DbCoNtext 和 DbSet 方法

許多方法（包括 `Add` 、 `Update` 、 `Attach` 和 `Remove` ）都有 <xref:Microsoft.EntityFrameworkCore.DbSet%601> 和 <xref:Microsoft.EntityFrameworkCore.DbContext> 的實作為。 這些方法對一般實體類型具有 _完全相同的行為_ 。 這是因為實體的 CLR 型別會對應至 EF Core 模型中的單一實體類型。 因此，CLR 型別會完整定義實體放在模型中的位置，因此可以隱含地判斷要使用的 DbSet。

這項規則的例外狀況是在使用 EF Core 5.0 引進的共用類型實體類型時，主要是針對多對多的聯結實體。 使用共用類型實體類型時，必須先針對正在使用的 EF Core 模型類型建立 DbSet。 `Add` `Update` `Attach` 然後，可以在 DbSet 上使用、、和等方法， `Remove` 而不會對使用的 EF Core 模型類型造成任何混淆。

針對多對多關聯性中的聯結實體，預設會使用共用類型的實體類型。 您也可以明確地設定共用類型實體類型，以便在多對多關聯性中使用。 例如，下列程式碼會設定 `Dictionary<string, int>` 為聯結實體類型：

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder
                .SharedTypeEntity<Dictionary<string, int>>(
                    "PostTag",
                    b =>
                        {
                            b.IndexerProperty<int>("TagId");
                            b.IndexerProperty<int>("PostId");
                        });

            modelBuilder.Entity<Post>()
                .HasMany(p => p.Tags)
                .WithMany(p => p.Posts)
                .UsingEntity<Dictionary<string, int>>(
                    "PostTag",
                    j => j.HasOne<Tag>().WithMany(),
                    j => j.HasOne<Post>().WithMany());
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/Samples.cs?name=OnModelCreating)]

[變更外鍵和](xref:core/change-tracking/relationship-changes) 導覽會顯示如何藉由追蹤新的聯結實體實例來關聯兩個實體。 下列程式碼會針對 `Dictionary<string, int>` 用於聯結實體的共用類型實體類型執行此動作：

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            var joinEntitySet = context.Set<Dictionary<string, int>>("PostTag");
            var joinEntity = new Dictionary<string, int>
            {
                ["PostId"] = post.Id,
                ["TagId"] = tag.Id
            };
            joinEntitySet.Add(joinEntity);

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);

            context.SaveChanges();
-->
[!code-csharp[DbContext_versus_DbSet_methods_1](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/Samples.cs?name=DbContext_versus_DbSet_methods_1)]

請注意，這 <xref:Microsoft.EntityFrameworkCore.DbContext.Set%60%601(System.String)?displayProperty=nameWithType> 是用來建立 `PostTag` 實體類型的 DbSet。 然後可以使用這個 DbSet，以 `Add` 新的聯結實體實例來呼叫。

> [!IMPORTANT]
> 依慣例用於聯結實體類型的 CLR 型別，在未來的版本中可能會變更，以改善效能。 除非已明確設定為在上述程式碼中完成，否則請勿相依于任何特定的聯結實體類型 `Dictionary<string, int>` 。

## <a name="property-versus-field-access"></a>屬性與欄位存取

從 EF Core 3.0 開始，實體屬性的存取預設會使用屬性的支援欄位。 這是有效率的，可避免觸發呼叫屬性 getter 和 setter 的副作用。 例如，這是消極式載入能夠避免觸發無限迴圈的方式。 如需有關在模型中設定支援欄位的詳細資訊，請參閱 [支援欄位](xref:core/modeling/backing-field) 。

有時候 EF Core 在修改屬性值時，可能需要產生副作用。 例如，將資料系結至實體時，設定屬性可能會產生通知至 U.I。 直接設定欄位時不會發生這種情況。 您可以藉由變更的來達成此目的 <xref:Microsoft.EntityFrameworkCore.PropertyAccessMode> ：

- 模型中的所有實體類型（使用 <xref:Microsoft.EntityFrameworkCore.ModelBuilder.UsePropertyAccessMode%2A?displayProperty=nameWithType>
- 特定實體類型的所有屬性和導覽（使用 <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.EntityTypeBuilder%601.UsePropertyAccessMode%2A?displayProperty=nameWithType>
- 使用的特定屬性 <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.UsePropertyAccessMode%2A?displayProperty=nameWithType>
- 使用的特定流覽 <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.NavigationBuilder.UsePropertyAccessMode%2A?displayProperty=nameWithType>

屬性存取模式 `Field` ， `PreferField` 會導致 EF Core 透過其支援欄位來存取屬性值。 同樣 `Property` 地，和 `PreferProperty` 將會導致 EF Core 透過其 getter 和 setter 存取屬性值。

如果 `Field` 使用或， `Property` 而且 ef core 無法分別透過 field 或 property getter/setter 存取值，ef core 將會擲回例外狀況。 這可確保當您認為 EF Core 時，一律會使用欄位/屬性存取。

另一方面， `PreferField` `PreferProperty` 如果無法使用慣用的存取，和模式將會切換回使用屬性或支援欄位。 `PreferField` 是 EF Core 3.0 的預設值。 這表示 EF Core 會盡可能使用欄位，但如果屬性必須透過 getter 或 setter 來存取，則不會失敗。

`FieldDuringConstruction` 並 `PreferFieldDuringConstruction` 設定 EF Core _只在建立實體實例時_ 使用支援欄位。 這可讓您在沒有 getter 和 setter 副作用的情況下執行查詢，而 EF Core 稍後的屬性變更將會造成這些副作用。 `PreferFieldDuringConstruction` 是 EF Core 3.0 之前的預設值。

下表摘要說明不同的屬性存取模式：

| PropertyAccessMode              | 喜好設定 | 建立實體的喜好設定 | 後援 | 建立實體的 Fallback
|:--------------------------------|------------|------------------------------|----------|---------------------------
| `Field`                         | 欄位      | 欄位                        | 擲回   | 擲回
| `Property`                      | 屬性   | 屬性                     | 擲回   | 擲回
| `PreferField`                   | 欄位      | 欄位                        | 屬性 | 屬性
| `PreferProperty`                | 屬性   | 屬性                     | 欄位    | 欄位
| `FieldDuringConstruction`       | 屬性   | 欄位                        | 欄位    | 擲回
| `PreferFieldDuringConstruction` | 屬性   | 欄位                        | 欄位    | 屬性

## <a name="temporary-values"></a>暫存值

當呼叫 SaveChanges 時，EF Core 會在追蹤將擁有資料庫所產生之實際索引鍵值的新實體時，建立暫存索引鍵值。 如需如何使用這些暫存值的總覽，請參閱 [EF Core 中的變更追蹤](xref:core/change-tracking/index) 。

### <a name="accessing-temporary-values"></a>存取臨時值

從 EF Core 3.0 開始，暫存值會儲存在變更追蹤器中，而不會直接設定在實體實例上。 不過，使用各種機制來 [存取追蹤的實體](xref:core/change-tracking/entity-entries)時，_會_ 公開這些暫存值。 例如，下列程式碼會使用下列程式碼來存取暫存值 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.CurrentValues%2A?displayProperty=nameWithType> ：

<!--
        using var context = new BlogsContext();

        var blog = new Blog { Name = ".NET Blog" };

        context.Add(blog);

        Console.WriteLine($"Blog.Id set on entity is {blog.Id}");
        Console.WriteLine($"Blog.Id tracked by EF is {context.Entry(blog).Property(e => e.Id).CurrentValue}");
-->
[!code-csharp[Temporary_values_1](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/Samples.cs?name=Temporary_values_1)]

此程式碼的輸出為：

```output
Blog.Id set on entity is 0
Blog.Id tracked by EF is -2147482643
```

<xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary%2A?displayProperty=nameWithType> 可以用來檢查臨時值。

### <a name="manipulating-temporary-values"></a>操作暫存值

明確地使用暫存值有時會很有用。 例如，可能會在 web 用戶端上建立新實體的集合，然後再序列化回伺服器。 外鍵值是設定這些實體之間關聯性的一種方式。 下列程式碼會使用這種方法，將新實體的圖形關聯到外鍵，同時仍然允許在呼叫 SaveChanges 時產生實際的索引鍵值。

<!--
            var blogs = new List<Blog>
            {
                new Blog { Id = -1, Name = ".NET Blog" },
                new Blog { Id = -2, Name = "Visual Studio Blog" }
            };

            var posts = new List<Post>()
            {
                new Post
                {
                    Id = -1,
                    BlogId = -1,
                    Title = "Announcing the Release of EF Core 5.0",
                    Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                },
                new Post
                {
                    Id = -2,
                    BlogId = -2,
                    Title = "Disassembly improvements for optimized managed debugging",
                    Content = "If you are focused on squeezing out the last bits of performance for your .NET service or..."
                }
            };

            using var context = new BlogsContext();

            foreach (var blog in blogs)
            {
                context.Add(blog).Property(e => e.Id).IsTemporary = true;
            }

            foreach (var post in posts)
            {
                context.Add(post).Property(e => e.Id).IsTemporary = true;
            }

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);

            context.SaveChanges();

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Temporary_values_2](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/Samples.cs?name=Temporary_values_2)]

請注意：

- 負數會用來做為暫存索引鍵值;這並不是必要的，但這是防止金鑰衝突的常見慣例。
- `Post.BlogId`FK 屬性會被指派與相關聯的 blog 的 PK 相同的負數值。
- 在 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary%2A> 追蹤每個實體之後，會將 PK 值標示為暫時性。 這是必要的，因為應用程式所提供的任何索引鍵值都會假設為真正的索引鍵值。

在呼叫 SaveChanges 之前查看 [變更追蹤器調試](xref:core/change-tracking/debug-views) 程式，會顯示 PK 值標示為暫時，而貼文會與正確的 blog 相關聯，包括瀏覽器的修正：

```output
Blog {Id: -2} Added
  Id: -2 PK Temporary
  Name: 'Visual Studio Blog'
  Posts: [{Id: -2}]
Blog {Id: -1} Added
  Id: -1 PK Temporary
  Name: '.NET Blog'
  Posts: [{Id: -1}]
Post {Id: -2} Added
  Id: -2 PK Temporary
  BlogId: -2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: -2}
  Tags: []
Post {Id: -1} Added
  Id: -1 PK Temporary
  BlogId: -1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: -1}
```

在呼叫之後 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> ，這些暫存值已由資料庫產生的實際值取代：

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}]
Blog {Id: 2} Unchanged
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Posts: [{Id: 2}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: []
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 2}
  Tags: []
```

## <a name="working-with-default-values"></a>使用預設值

EF Core 可讓屬性在呼叫時，從資料庫取得其預設值 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> 。 如同產生的索引鍵值，EF Core 只會在未明確設定任何值的情況下，從資料庫使用預設值。 例如，請考慮下列實體類型：

<!--
    public class Token
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public DateTime ValidFrom { get; set; }
    }
-->
[!code-csharp[Token](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Token)]

`ValidFrom`屬性設定為從資料庫取得預設值：

<!--
        modelBuilder
            .Entity<Token>()
            .Property(e => e.ValidFrom)
            .HasDefaultValueSql("CURRENT_TIMESTAMP");
-->
[!code-csharp[OnModelCreating_Token](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=OnModelCreating_Token)]

當您插入此類型的實體時，除非改為設定明確的值，否則 EF Core 會讓資料庫產生該值。 例如：

<!--
            using var context = new BlogsContext();

            context.AddRange(
                new Token { Name = "A" },
                new Token { Name = "B", ValidFrom = new DateTime(1111, 11, 11, 11, 11, 11)});

            context.SaveChanges();

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Working_with_default_values_1](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Working_with_default_values_1)]

查看 [ [變更追蹤](xref:core/change-tracking/debug-views) 器] 偵錯工具，會顯示資料庫所產生的第一個 token `ValidFrom` ，而第二個權杖則使用明確設定的值：

```output
Token {Id: 1} Unchanged
  Id: 1 PK
  Name: 'A'
  ValidFrom: '12/30/2020 6:36:06 PM'
Token {Id: 2} Unchanged
  Id: 2 PK
  Name: 'B'
  ValidFrom: '11/11/1111 11:11:11 AM'
```

> [!NOTE]
> 若要使用資料庫預設值，資料庫資料行必須已設定預設值條件約束。 使用或時，EF Core 遷移會自動完成這項工作 <xref:Microsoft.EntityFrameworkCore.RelationalPropertyBuilderExtensions.HasDefaultValueSql%2A> <xref:Microsoft.EntityFrameworkCore.RelationalPropertyBuilderExtensions.HasDefaultValue%2A> 。 在不使用 EF Core 遷移的情況下，請務必以其他方式在資料行上建立預設條件約束。

### <a name="using-nullable-properties"></a>使用可為 null 的屬性

EF Core 可以比較屬性值與該類型的 CLR 預設值，以判斷是否已設定屬性。 這在大多數情況下都能順利運作，但表示 CLR 預設值無法明確地插入資料庫中。 例如，假設有一個具有整數屬性的實體：

<!--
public class Foo1
{
    public int Id { get; set; }
    public int Count { get; set; }
}
-->
[!code-csharp[Foo1](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Foo1)]

該屬性設定為具有-1 的資料庫預設值：

<!--
        modelBuilder
            .Entity<Foo1>()
            .Property(e => e.Count)
            .HasDefaultValue(-1);
-->
[!code-csharp[OnModelCreating_Foo1](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=OnModelCreating_Foo1)]

其目的是只要未設定明確的值，就會使用預設值-1。 不過，將值設定為 0 (整數的 CLR 預設值) 與 EF Core 無關，不會設定任何值，這表示不可能為這個屬性插入0。 例如：

<!--
        using var context = new BlogsContext();

        var fooA = new Foo1 { Count = 10 };
        var fooB = new Foo1 { Count = 0 };
        var fooC = new Foo1 { };

        context.AddRange(fooA, fooB, fooC);
        context.SaveChanges();

        Debug.Assert(fooA.Count == 10);
        Debug.Assert(fooB.Count == -1); // Not what we want!
        Debug.Assert(fooC.Count == -1);
-->
[!code-csharp[Working_with_default_values_2](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Working_with_default_values_2)]

請注意， `Count` 已明確設定為0的實例仍會從資料庫中取得預設值，這不是我們想要的值。 處理此情況的簡單方法是讓 `Count` 屬性變成可為 null：

<!--
public class Foo2
{
    public int Id { get; set; }
    public int? Count { get; set; }
}
-->
[!code-csharp[Foo2](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Foo2)]

這會使 CLR 預設為 null，而不是0，這表示現在會在明確設定時插入0：

<!--
        using var context = new BlogsContext();

        var fooA = new Foo2 { Count = 10 };
        var fooB = new Foo2 { Count = 0 };
        var fooC = new Foo2 { };

        context.AddRange(fooA, fooB, fooC);
        context.SaveChanges();

        Debug.Assert(fooA.Count == 10);
        Debug.Assert(fooB.Count == 0);
        Debug.Assert(fooC.Count == -1);
-->
[!code-csharp[Working_with_default_values_3](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Working_with_default_values_3)]

### <a name="using-nullable-backing-fields"></a>使用可為 null 的支援欄位

> [!NOTE]
> EF Core 5.0 和更新版本支援這個可為 null 的支援欄位模式。

讓屬性成為可為 null 的問題，在領域模型中可能會變成可為 null。 強制屬性可為 null，因此會危及模型。

從 EF Core 5.0 開始，此屬性可保留為不可為 null，而且只有支援欄位可為 null。 例如：

<!--
public class Foo3
{
    public int Id { get; set; }

    private int? _count;
    public int Count
    {
        get => _count ?? -1;
        set => _count = value;
    }
}
-->
[!code-csharp[Foo3](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Foo3)]

如果將屬性明確設定為0，就可以插入 CLR 預設 (0) ，而不需要將屬性公開為網域模型中的可為 null。 例如：

<!--
            using var context = new BlogsContext();

            var fooA = new Foo3 { Count = 10 };
            var fooB = new Foo3 { Count = 0 };
            var fooC = new Foo3 { };

            context.AddRange(fooA, fooB, fooC);
            context.SaveChanges();

            Debug.Assert(fooA.Count == 10);
            Debug.Assert(fooB.Count == 0);
            Debug.Assert(fooC.Count == -1);
-->
[!code-csharp[Working_with_default_values_4](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Working_with_default_values_4)]

#### <a name="nullable-backing-fields-for-bool-properties"></a>Bool 屬性可為 null 的支援欄位

當搭配使用 bool 屬性與存放區產生的預設值時，此模式特別有用。 因為的 CLR 預設值 `bool` 為 "false"，表示無法使用一般模式明確插入 "false"。 例如，請考慮使用 `User` 實體類型：

<!--
public class User
{
    public int Id { get; set; }
    public string Name { get; set; }

    private bool? _isAuthorized;
    public bool IsAuthorized
    {
        get => _isAuthorized ?? true;
        set => _isAuthorized = value;
    }
}
-->
[!code-csharp[User](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=User)]

`IsAuthorized`屬性的設定方式是將資料庫預設值設為 "true"：

<!--
        modelBuilder
            .Entity<User>()
            .Property(e => e.IsAuthorized)
            .HasDefaultValue(true);
-->
[!code-csharp[OnModelCreating_User](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=OnModelCreating_User)]

在 `IsAuthorized` 插入之前，屬性可以明確地設定為 "true" 或 "false"，或者可以保留為未設定，在此情況下會使用資料庫預設值：

<!--
        using var context = new BlogsContext();

        var userA = new User { Name = "Mac" };
        var userB = new User { Name = "Alice", IsAuthorized = true };
        var userC = new User { Name = "Baxter", IsAuthorized = false }; // Always deny Baxter access!

        context.AddRange(userA, userB, userC);

        context.SaveChanges();
-->
[!code-csharp[Working_with_default_values_5](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Working_with_default_values_5)]

使用 SQLite 時，SaveChanges 的輸出會顯示資料庫預設值用於 Mac，而明確值則是針對 Alice 和 Baxter 而設定：

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='Mac' (Size = 3)], CommandType='Text', CommandTimeout='30']
INSERT INTO "User" ("Name")
VALUES (@p0);
SELECT "Id", "IsAuthorized"
FROM "User"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();

-- Executed DbCommand (0ms) [Parameters=[@p0='True' (DbType = String), @p1='Alice' (Size = 5)], CommandType='Text', CommandTimeout='30']
INSERT INTO "User" ("IsAuthorized", "Name")
VALUES (@p0, @p1);
SELECT "Id"
FROM "User"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();

-- Executed DbCommand (0ms) [Parameters=[@p0='False' (DbType = String), @p1='Baxter' (Size = 6)], CommandType='Text', CommandTimeout='30']
INSERT INTO "User" ("IsAuthorized", "Name")
VALUES (@p0, @p1);
SELECT "Id"
FROM "User"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

### <a name="schema-defaults-only"></a>僅限架構預設值

有時候，在 EF Core 遷移所建立的資料庫架構中使用預設值，而沒有 EF Core 使用這些值來進行插入，會很有用。 您可以藉由設定屬性來達成此目的， <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.ValueGeneratedNever%2A?displayProperty=nameWithType> 例如：

<!--
        modelBuilder
            .Entity<Bar>()
            .Property(e => e.Count)
            .HasDefaultValue(-1)
            .ValueGeneratedNever();
-->
[!code-csharp[OnModelCreating_Bar](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=OnModelCreating_Bar)]
