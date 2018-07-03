---
title: 關聯性的 EF Core
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 0ff736a3-f1b0-4b58-a49c-4a7094bd6935
ms.technology: entity-framework-core
uid: core/modeling/relationships
ms.openlocfilehash: 1732d32643effb0f12111191ed4ba3abb4182d93
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2017
ms.locfileid: "26053028"
---
# <a name="relationships"></a>關聯性

關聯性定義的兩個實體如何與彼此相關。 在關聯式資料庫中，這是外部索引鍵條件約束所表示。

> [!NOTE]  
> 大部分的這篇文章中的範例會使用一對多關聯性，來示範概念。 如需一對一和多對多關聯性的範例，請參閱[其他關聯性模式](#other-relationship-patterns)發行項的最後一節。

## <a name="definition-of-terms"></a>詞彙定義

有許多的詞彙用於描述關聯性

* **相依的實體：** 這是包含外部索引鍵屬性的實體。 有時稱為 'child' 的關聯性。

* **主要實體：** 這是包含的主要/替代索引鍵屬性的實體。 有時稱為 'parent' 的關聯性。

* **外部索引鍵：** 中用來儲存與相關實體的主要索引鍵內容值的相依實體屬性。

* **主體的金鑰：** 屬性可唯一識別與主要實體。 這可能是主索引鍵或替代索引鍵。

* **導覽屬性：** 包含相關的公司實體參考的主體和 （或） 相依實體上定義的屬性。

  * **集合導覽屬性：** 導覽屬性，其中包含許多相關實體的參考。

  * **參考導覽屬性：** 保留單一相關實體的參考導覽屬性。

  * **反向導覽屬性：** 時討論特定的導覽屬性時，此一詞是指關聯性另一端的導覽屬性。

下列程式碼清單會顯示一對多關聯性之間`Blog`和`Post`

* `Post`為相依實體

* `Blog`是主要實體

* `Post.BlogId`外部索引鍵

* `Blog.BlogId`是 （在此情況下它是主索引鍵，而不是替代索引鍵） 的主要索引鍵

* `Post.Blog`是參考導覽屬性

* `Blog.Posts`是集合導覽屬性

* `Post.Blog`是反向導覽屬性的`Blog.Posts`（反之亦然）

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/Relationships/Full.cs#Entities)]

## <a name="conventions"></a>慣例

依照慣例，在類型上探索到的導覽屬性時將會建立關聯性。 屬性會被視為導覽屬性，如果它所指向的類型可以對應為純量型別目前的資料庫提供者。

> [!NOTE]  
> 關聯性探索到的慣例會永遠以目標主要實體的主索引鍵。 若要以其他的索引鍵為目標，額外的設定必須使用 Fluent API 執行。

### <a name="fully-defined-relationships"></a>完整定義的關聯性

關聯性的最常見的模式是已定義這兩個端點的關聯性和相依的實體類別中定義的外部索引鍵屬性的導覽屬性。

* 如果找不到導覽屬性配對是兩個類型之間，它們將會設定為相同關聯性的反向導覽屬性。

* 如果相依的實體包含一個名為屬性`<primary key property name>`， `<navigation property name><primary key property name>`，或`<principal entity name><primary key property name>`接著，它會設定為外部索引鍵。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/Relationships/Full.cs?name=Entities&highlight=6,15,16)]

> [!WARNING]  
> 如果有多個定義兩個類型之間的導覽屬性 (也就是多個相異點彼此的巡覽組) 慣例再建立任何關聯性，您必須手動將其設定為識別方式導覽屬性配對。

### <a name="no-foreign-key-property"></a>沒有外部索引鍵屬性

雖然建議有相依的實體類別中定義的外部索引鍵屬性，並不需要。 如果不找到任何外部索引鍵屬性，將介紹陰影的外部索引鍵屬性具有名稱`<navigation property name><principal key property name>`(請參閱[遮蔽屬性](shadow-properties.md)如需詳細資訊)。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/Relationships/NoForeignKey.cs?name=Entities&highlight=6,15)]

### <a name="single-navigation-property"></a>單一瀏覽屬性

包括一個導覽屬性 （沒有反向導覽，以及任何外部索引鍵屬性），即已足夠有依慣例定義關聯性。 您也可以讓單一導覽屬性和外部索引鍵屬性。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/Relationships/OneNavigation.cs?name=Entities&highlight=6)]

### <a name="cascade-delete"></a>串聯刪除

依照慣例，cascade delete 將會設定為*Cascade*必要的關聯性和*ClientSetNull*選擇性的關聯性。 *Cascade*表示相依實體也會一併刪除。 *ClientSetNull*方法不會載入記憶體的相依實體將會保留不變且必須以手動方式刪除，或更新為指向有效的主要實體。 載入到記憶體的實體，EF Core 會嘗試將外部索引鍵屬性設定為 null。

請參閱[必要和選擇性的關聯性](#required-and-optional-relationships)必要和選擇性的關聯性之間的差異 > 一節。

請參閱[Cascade Delete](../saving/cascade-delete.md)的更多詳細的不同刪除行為和使用慣例的預設值。

## <a name="data-annotations"></a>資料註釋

有兩個可用來設定關聯性的資料註解`[ForeignKey]`和`[InverseProperty]`。

### <a name="foreignkey"></a>[ForeignKey]

若要設定哪些屬性應作為外部索引鍵屬性指定的關聯性，您可以使用資料註解。 這通常是依照慣例探索不到外部索引鍵屬性時。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/Relationships/ForeignKey.cs?name=Entities&highlight=17)]

> [!TIP]  
> `[ForeignKey]`註解可以放在關聯性中的任一個導覽屬性。 不需要相依的實體類別中的導覽屬性上移。

### <a name="inverseproperty"></a>[InverseProperty]

您可以使用資料註解來設定相依及主體實體上的導覽屬性如何配對。 這通常是一對以上兩個實體類型之間的導覽屬性時。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/Relationships/InverseProperty.cs?name=Entities&highlight=20,23)]

## <a name="fluent-api"></a>關於 fluent 應用程式開發介面

若要設定關聯性 Fluent API 中，您必須啟動用來識別組成關聯性的導覽屬性。 `HasOne`或`HasMany`識別您在開始設定的實體類型上的導覽屬性。 您接著鏈結呼叫`WithOne`或`WithMany`來識別反向導覽。 `HasOne`/`WithOne`用於參考導覽屬性和`HasMany` / `WithMany`用於集合導覽屬性。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Relationships/NoForeignKey.cs?name=Model&highlight=8,9,10)]

### <a name="single-navigation-property"></a>單一瀏覽屬性

如果您只需要一個導覽屬性，則有無參數多載的`WithOne`和`WithMany`。 這表示有在概念上是參考或關聯性另一端的集合，但沒有包含在實體類別中的導覽屬性。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Relationships/OneNavigation.cs?name=Model&highlight=10)]

### <a name="foreign-key"></a>外部索引鍵

您可以使用 fluent 應用程式開發的應用程式開發介面來設定哪些屬性應為外部索引鍵屬性用於指定關聯性。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Relationships/ForeignKey.cs?name=Model&highlight=11)]

下列程式碼清單示範如何設定複合外部索引鍵。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Relationships/CompositeForeignKey.cs?name=Model&highlight=13)]

您可以使用的字串多載`HasForeignKey(...)`陰影屬性設定為外部索引鍵 (請參閱[遮蔽屬性](shadow-properties.md)如需詳細資訊)。 我們建議您明確陰影屬性加入至模型之前使用的外部索引鍵為 （如下所示）。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Relationships/ShadowForeignKey.cs#Sample)]

### <a name="principal-key"></a>主體金鑰

如果您想要參考的屬性以外的主索引鍵的外部索引鍵，您可以使用 fluent 應用程式開發的應用程式開發介面來設定關聯性的主要索引鍵內容。 您將設定為主要索引鍵會自動屬性是做為替代索引鍵的安裝程式 (請參閱[替代索引鍵](alternate-keys.md)如需詳細資訊)。

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

下列程式碼清單示範如何設定複合主體索引鍵。

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

您可以設定關聯性是否必要或選擇性使用 fluent 應用程式開發的應用程式開發介面。 最終這會控制外部索引鍵屬性是必要或選擇性。 當您使用陰影狀態外部索引鍵，這是最有用。 如果您有實體類別中的外部索引鍵屬性，則關聯性的 requiredness 取決於外部索引鍵屬性是必要或選擇性 (請參閱[必要與選用屬性](required-optional.md)如需詳細資訊資訊）。

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

請參閱[Cascade Delete](../saving/cascade-delete.md)上儲存的資料區段的每個選項的詳細討論。

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

一對一關聯性兩端具有參考導覽屬性。 它們遵循相同的慣例，作為一對多關聯性，但外部索引鍵屬性，以確保只有一個相關與每個主體上引進唯一索引。

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
> EF 會選擇其中一個要能夠偵測到外部索引鍵屬性為基礎的相依實體。 如果不正確的實體會被選為相依項，您可以使用 fluent 應用程式開發的應用程式開發介面來修正這個問題。

設定關聯性 fluent 應用程式開發的應用程式開發介面，當您使用`HasOne`和`WithOne`方法。

設定您需要指定的相依實體類型的外部索引鍵時發現提供給泛型參數`HasForeignKey`下面的清單中。 在一對多關聯性會清除使用參考瀏覽該實體是相依，而與集合是主體。 但這不是一對一的關聯性-因此，不需要明確定義它。

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

尚不支援多對多關聯性沒有實體類別，以代表聯結的資料表。 不過，您可以藉由聯結資料表和對應兩個不同的一對多關聯性的實體類別表示多對多關聯性。

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
