---
title: 關聯性的 EF Core
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 0ff736a3-f1b0-4b58-a49c-4a7094bd6935
ms.technology: entity-framework-core
uid: core/modeling/relationships
ms.openlocfilehash: 0e60ade605ffb73b02934ea32f1c757affce970d
ms.sourcegitcommit: bdd06c9a591ba5e6d6a3ec046c80de98f598f3f3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2018
ms.locfileid: "37949214"
---
# <a name="relationships"></a>關聯性

關聯性會定義兩個實體如何與彼此相關。 在關聯式資料庫中，這被以外部索引鍵條件約束。

> [!NOTE]  
> 大部分的這篇文章中的範例會使用一個對多關聯性，來示範概念。 如需一對一和多對多關聯性的範例，請參閱[其他關聯性模式](#other-relationship-patterns)文章的最後一節。

## <a name="definition-of-terms"></a>詞彙定義

有幾個名詞，用來描述關聯性

* **相依的實體：** 這是包含外部索引鍵屬性的實體。 有時稱為 「 子 」 的關聯性。

* **主要實體：** 這是包含的主要/替代索引鍵屬性的實體。 有時稱為 'parent' 的關聯性。

* **外部索引鍵：** 中用來儲存與相關實體的主要索引鍵屬性的值為相依實體屬性。

* **主體的金鑰：** 唯一識別主要實體的屬性。 這可能是主索引鍵或替代索引鍵。

* **導覽屬性：** 包含相關的公司實體參考的主體和 （或） 相依實體上定義的屬性。

  * **集合導覽屬性：** 包含許多相關的實體參考的瀏覽屬性。

  * **參考導覽屬性：** 保留單一相關實體的參考的瀏覽屬性。

  * **反向的導覽屬性：** 時討論特定的導覽屬性，這個詞彙是指關聯性另一端的導覽屬性。

下列程式碼清單會顯示一對多關聯性之間`Blog`和 `Post`

* `Post` 是相依實體

* `Blog` 是主要實體

* `Post.BlogId` 外部索引鍵

* `Blog.BlogId` 是 （在此情況下它是主索引鍵，而不是替代索引鍵） 的主體金鑰

* `Post.Blog` 是參考導覽屬性

* `Blog.Posts` 集合導覽屬性

* `Post.Blog` 是的反向的導覽屬性`Blog.Posts`（反之亦然）

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/Relationships/Full.cs#Entities)]

## <a name="conventions"></a>慣例

依照慣例，在類型上探索到的導覽屬性時，將會建立關聯性。 屬性會被視為一個導覽屬性，如果它所指向的類型不能為純量型別對應目前的資料庫提供者。

> [!NOTE]  
> 慣例所探索到的關聯性會一律以目標主要實體的主索引鍵。 為目標的其他索引鍵，額外的組態必須使用 Fluent API 執行。

### <a name="fully-defined-relationships"></a>完整定義的關聯性

關聯性的最常見的模式是將這兩個端點的關聯性和相依的實體類別中定義的外部索引鍵屬性定義的導覽屬性。

* 如果兩個類型之間找到對導覽屬性，則它們會被設定為相同關聯性的反向的導覽屬性。

* 如果相依的實體包含屬性，名為`<primary key property name>`， `<navigation property name><primary key property name>`，或`<principal entity name><primary key property name>`接著，它會設定為外部索引鍵。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/Relationships/Full.cs?name=Entities&highlight=6,15,16)]

> [!WARNING]  
> 如果有多個定義兩個類型之間的導覽屬性 (也就是指向彼此的巡覽的多個不同的組)，然後依照慣例建立任何關聯性和您必須手動將其設定為識別如何導覽屬性配對。

### <a name="no-foreign-key-property"></a>沒有外部索引鍵屬性

雖然建議有相依的實體類別中定義的外部索引鍵屬性，並不需要。 如果不找到任何外部索引鍵屬性，則會介紹陰影的外部索引鍵屬性同名`<navigation property name><principal key property name>`(請參閱 <<c2> [ 遮蔽屬性](shadow-properties.md)如需詳細資訊)。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/Relationships/NoForeignKey.cs?name=Entities&highlight=6,15)]

### <a name="single-navigation-property"></a>單一導覽屬性

包括一個導覽屬性 （未反轉的導覽中和任何外部索引鍵屬性），就足以具有由慣例定義關聯性。 您也可以有單一導覽屬性和外部索引鍵屬性。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/Relationships/OneNavigation.cs?name=Entities&highlight=6)]

### <a name="cascade-delete"></a>串聯刪除

依照慣例，串聯刪除將會設定為*Cascade*必要的關聯性並*ClientSetNull*選擇性的關聯性。 *Cascade*表示也會刪除相依實體。 *ClientSetNull*方法不會載入到記憶體的相依實體將會保留不變且必須以手動方式刪除，或更新為指向有效的主體實體。 載入到記憶體的實體，EF Core 會嘗試將外部索引鍵屬性設定為 null。

請參閱[必要和選擇性的關聯性](#required-and-optional-relationships)一節以取得必要和選擇性的關聯性之間的差異。

請參閱[串聯刪除](../saving/cascade-delete.md)的更多詳細的不同刪除行為和使用慣例的預設值。

## <a name="data-annotations"></a>資料註釋

有兩個可用來設定關聯性的資料註解`[ForeignKey]`和`[InverseProperty]`。

### <a name="foreignkey"></a>[ForeignKey]

若要設定哪些屬性應為外部索引鍵屬性用於指定的關聯性，您可以使用資料註解。 這通常是依照慣例找不到外部索引鍵屬性時。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/Relationships/ForeignKey.cs?name=Entities&highlight=17)]

> [!TIP]  
> `[ForeignKey]`可以在其中一個關聯性中的導覽屬性上放置註解。 它不需要相依的實體類別中的導覽屬性上移。

### <a name="inverseproperty"></a>[InverseProperty]

若要設定的相依及主體實體的導覽屬性如何配對，您可以使用資料註解。 這通常是多個對兩個實體類型之間的導覽屬性時。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/Relationships/InverseProperty.cs?name=Entities&highlight=20,23)]

## <a name="fluent-api"></a>Fluent API

若要設定關聯性，Fluent API 中，您會開始藉由識別組成關聯性的導覽屬性。 `HasOne` 或`HasMany`識別您開始組態的實體類型上的導覽屬性。 您接著鏈結的呼叫`WithOne`或`WithMany`識別反向導覽。 `HasOne`/`WithOne` 用於參考導覽屬性和`HasMany` / `WithMany`用於集合導覽屬性。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Relationships/NoForeignKey.cs?name=Model&highlight=8,9,10)]

### <a name="single-navigation-property"></a>單一導覽屬性

如果您只需要一個導覽屬性，則有無參數多載`WithOne`和`WithMany`。 這表示有在概念上是參考或關聯性另一端的集合，但未包含在實體類別中的導覽屬性。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Relationships/OneNavigation.cs?name=Model&highlight=10)]

### <a name="foreign-key"></a>外部索引鍵

您可以使用 Fluent API，若要設定哪些屬性應為外部索引鍵屬性用於指定的關聯性。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Relationships/ForeignKey.cs?name=Model&highlight=11)]

下列程式碼清單示範如何設定複合外部索引鍵。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Relationships/CompositeForeignKey.cs?name=Model&highlight=13)]

您可以使用的字串多載`HasForeignKey(...)`若要設定陰影屬性作為外部索引鍵 (請參閱 <<c2> [ 遮蔽屬性](shadow-properties.md)如需詳細資訊)。 我們建議您明確地將陰影屬性加入模型之前使用的外部索引鍵 （如下所示）。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Relationships/ShadowForeignKey.cs#Sample)]

### <a name="principal-key"></a>主體金鑰

如果您想要參考的屬性以外的主索引鍵的外部索引鍵時，您可以使用 Fluent API 來設定主體的索引鍵屬性關聯性。 主體的金鑰會自動設定的屬性是做為替代索引鍵的安裝程式 (請參閱[替代索引鍵](alternate-keys.md)如需詳細資訊)。

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/Relationships/PrincipalKey.cs?highlight=11)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Car> Cars { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<RecordOfSale>()
            .HasOne(s => s.Car)
            .WithMany(c => c.SaleHistory)
            .HasForeignKey(s => s.CarLicensePlate)
            .HasPrincipalKey(c => c.LicensePlate);
    }
}

public class Car
{
    public int CarId { get; set; }
    public string LicensePlate { get; set; }
    public string Make { get; set; }
    public string Model { get; set; }

    public List<RecordOfSale> SaleHistory { get; set; }
}

public class RecordOfSale
{
    public int RecordOfSaleId { get; set; }
    public DateTime DateSold { get; set; }
    public decimal Price { get; set; }

    public string CarLicensePlate { get; set; }
    public Car Car { get; set; }
}
```

下列程式碼清單示範如何設定複合的主體索引鍵。

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/Relationships/CompositePrincipalKey.cs?highlight=11)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Car> Cars { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<RecordOfSale>()
            .HasOne(s => s.Car)
            .WithMany(c => c.SaleHistory)
            .HasForeignKey(s => new { s.CarState, s.CarLicensePlate })
            .HasPrincipalKey(c => new { c.State, c.LicensePlate });
    }
}

public class Car
{
    public int CarId { get; set; }
    public string State { get; set; }
    public string LicensePlate { get; set; }
    public string Make { get; set; }
    public string Model { get; set; }

    public List<RecordOfSale> SaleHistory { get; set; }
}

public class RecordOfSale
{
    public int RecordOfSaleId { get; set; }
    public DateTime DateSold { get; set; }
    public decimal Price { get; set; }

    public string CarState { get; set; }
    public string CarLicensePlate { get; set; }
    public Car Car { get; set; }
}
```

> [!WARNING]  
> 您可以在其中指定主體的索引鍵屬性的順序必須符合指定的外部索引鍵的順序。

### <a name="required-and-optional-relationships"></a>必要和選擇性的關聯性

您可以使用 Fluent API 來設定關聯性是否必要或選擇性。 最後這會控制外部索引鍵屬性是必要或選擇性。 當您使用陰影狀態外部索引鍵，這是最有用。 如果您有在您實體類別中的外部索引鍵屬性，則關聯性的 requiredness 取決於外部索引鍵屬性是必要或選擇性 (請參閱[必要和選擇性屬性](required-optional.md)如需詳細資訊資訊）。

<!-- [!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Relationships/Required.cs?highlight=11)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Post>()
            .HasOne(p => p.Blog)
            .WithMany(b => b.Posts)
            .IsRequired();
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }

    public List<Post> Posts { get; set; }
}

public class Post
{
    public int PostId { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public Blog Blog { get; set; }
}
```

### <a name="cascade-delete"></a>串聯刪除

您可以使用 Fluent API 來明確設定指定的關聯性的串聯刪除行為。

請參閱[串聯刪除](../saving/cascade-delete.md)上儲存的資料區段的每個選項的詳細討論。

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/Relationships/CascadeDelete.cs?highlight=11)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Post>()
            .HasOne(p => p.Blog)
            .WithMany(b => b.Posts)
            .OnDelete(DeleteBehavior.Cascade);
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }

    public List<Post> Posts { get; set; }
}

public class Post
{
    public int PostId { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public int? BlogId { get; set; }
    public Blog Blog { get; set; }
}
```

## <a name="other-relationship-patterns"></a>其他關聯性模式

### <a name="one-to-one"></a>一對一

一對一關聯性兩端有參考導覽屬性。 它們會遵循相同的慣例，作為一對多關聯性，但唯一索引在外部索引鍵屬性，以確保只有一個相依與每個主體中引進。

<!-- [!code-csharp[Main](samples/core/Modeling/Conventions/Samples/Relationships/OneToOne.cs?highlight=6,15,16)] -->
``` csharp
public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }

    public BlogImage BlogImage { get; set; }
}

public class BlogImage
{
    public int BlogImageId { get; set; }
    public byte[] Image { get; set; }
    public string Caption { get; set; }

    public int BlogId { get; set; }
    public Blog Blog { get; set; }
}
```

> [!NOTE]  
> EF 會選擇其中一個是它能夠偵測到外部索引鍵屬性為基礎的相依實體。 如果錯誤的實體會被選為相依，您可以使用 Fluent API，若要修正此問題。

設定使用 Fluent API 的關聯性，當您使用`HasOne`和`WithOne`方法。

設定您要指定的相依實體型別-外部索引鍵時注意到提供給泛型參數`HasForeignKey`在下方的清單。 在一對多關聯性很顯然，參考導覽的實體是相依，而與該集合是主體。 但這不是因此在一對一關聯性; 因此不需要明確地定義它。

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/Relationships/OneToOne.cs?highlight=11)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<BlogImage> BlogImages { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>()
            .HasOne(p => p.BlogImage)
            .WithOne(i => i.Blog)
            .HasForeignKey<BlogImage>(b => b.BlogForeignKey);
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }

    public BlogImage BlogImage { get; set; }
}

public class BlogImage
{
    public int BlogImageId { get; set; }
    public byte[] Image { get; set; }
    public string Caption { get; set; }

    public int BlogForeignKey { get; set; }
    public Blog Blog { get; set; }
}
```

### <a name="many-to-many"></a>多對多

尚不支援多對多關聯性，而不需要實體類別，以代表聯結的資料表。 不過，您可以透過包含聯結資料表的對應兩個個別一對多關聯性的實體類別來表示多對多關聯性。

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/Relationships/ManyToMany.cs?highlight=11,12,13,14,16,17,18,19,39,40,41,42,43,44,45,46)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Post> Posts { get; set; }
    public DbSet<Tag> Tags { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<PostTag>()
            .HasKey(t => new { t.PostId, t.TagId });

        modelBuilder.Entity<PostTag>()
            .HasOne(pt => pt.Post)
            .WithMany(p => p.PostTags)
            .HasForeignKey(pt => pt.PostId);

        modelBuilder.Entity<PostTag>()
            .HasOne(pt => pt.Tag)
            .WithMany(t => t.PostTags)
            .HasForeignKey(pt => pt.TagId);
    }
}

public class Post
{
    public int PostId { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public List<PostTag> PostTags { get; set; }
}

public class Tag
{
    public string TagId { get; set; }

    public List<PostTag> PostTags { get; set; }
}

public class PostTag
{
    public int PostId { get; set; }
    public Post Post { get; set; }

    public string TagId { get; set; }
    public Tag Tag { get; set; }
}
```
