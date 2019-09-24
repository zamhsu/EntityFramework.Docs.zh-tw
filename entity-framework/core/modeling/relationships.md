---
title: 關聯性-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 0ff736a3-f1b0-4b58-a49c-4a7094bd6935
uid: core/modeling/relationships
ms.openlocfilehash: 1e9c62bec47263ef452c7ac425a0bb446f9371d8
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197647"
---
# <a name="relationships"></a>關聯性

關聯性會定義兩個實體之間的關聯性。 在關係資料庫中，這是以 foreign key 條件約束來表示。

> [!NOTE]  
> 本文中的大部分範例都會使用一對多關聯性來示範概念。 如需一對一和多對多關聯性的範例，請參閱本文結尾的[其他關聯性模式](#other-relationship-patterns)一節。

## <a name="definition-of-terms"></a>詞彙的定義

有數個詞彙用來描述關聯性

* **相依實體：** 這是包含外鍵屬性的實體。 有時稱為關聯性的「子系」。

* **主體實體：** 這是包含主要/替代索引鍵屬性的實體。 有時稱為關聯性的「父系」。

* **外鍵：** 相依實體中的屬性，用來儲存與實體相關之主要索引鍵屬性的值。

* **主要金鑰：** 可唯一識別主體實體的屬性。 這可能是主要金鑰或替代索引鍵。

* **導覽屬性：** 在主體和/或相依實體上定義的屬性，其中包含相關實體的參考。

  * **集合導覽屬性：** 導覽屬性，其中包含許多相關實體的參考。

  * **參考導覽屬性：** 保存單一相關實體之參考的導覽屬性。

  * **反向導覽屬性：** 在討論特定導覽屬性時，此詞彙是指關聯性另一端的導覽屬性。

下列程式代碼清單顯示與之間`Blog`的一對多關聯性`Post`

* `Post`是相依實體

* `Blog`是主體實體

* `Post.BlogId`是外鍵

* `Blog.BlogId`是主要金鑰（在此案例中，它是主鍵，而不是替代索引鍵）

* `Post.Blog`是參考導覽屬性

* `Blog.Posts`是集合導覽屬性

* `Post.Blog`是的`Blog.Posts`反向導覽屬性（反之亦然）

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/Full.cs#Entities)]

## <a name="conventions"></a>慣例

依照慣例，在類型上發現導覽屬性時，將會建立關聯性。 如果所指向的型別無法由目前的資料庫提供者對應為純量型別，則會將屬性視為導覽屬性。

> [!NOTE]  
> 依照慣例探索到的關聯性一律會以主體實體的主要金鑰為目標。 若要以替代金鑰為目標，必須使用流暢的 API 來執行其他設定。

### <a name="fully-defined-relationships"></a>完整定義的關聯性

關聯性最常見的模式是讓關聯性兩端定義的導覽屬性，以及相依實體類別中定義的外鍵屬性。

* 如果在兩個類型之間找到一對導覽屬性，則會將它們設定為相同關聯性的反向導覽屬性。

* 如果相依實體包含名為`<primary key property name>`、或`<principal entity name><primary key property name>`的`<navigation property name><primary key property name>`屬性，則會將它設定為外鍵。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/Full.cs?name=Entities&highlight=6,15,16)]

> [!WARNING]  
> 如果在兩個類型之間定義了多個導覽屬性（也就是多個彼此指向的不同流覽），則不會依照慣例建立關聯性，而且您必須手動設定它們，以識別導覽屬性配對。

### <a name="no-foreign-key-property"></a>沒有外鍵屬性

雖然建議在相依實體類別中定義外鍵屬性，但它並不是必要的。 如果找不到任何外鍵屬性，則會以名稱`<navigation property name><principal key property name>`引進陰影外鍵屬性（如需詳細資訊，請參閱[陰影屬性](shadow-properties.md)）。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/NoForeignKey.cs?name=Entities&highlight=6,15)]

### <a name="single-navigation-property"></a>單一導覽屬性

只包含一個導覽屬性（沒有反向導覽，而且沒有外鍵屬性）就足以擁有依照慣例定義的關聯性。 您也可以有一個導覽屬性和一個外鍵屬性。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/OneNavigation.cs?name=Entities&highlight=6)]

### <a name="cascade-delete"></a>串聯刪除

依照慣例，串聯刪除會針對必要關聯性設定為*cascade* ，並針對選擇性關聯性*deletebehavior.clientsetnull* 。 *Cascade*表示也會刪除相依的實體。 *Deletebehavior.clientsetnull*表示未載入記憶體的相依實體會維持不變，必須手動刪除或更新，以指向有效的主體實體。 若為載入記憶體中的實體，EF Core 會嘗試將外鍵屬性設定為 null。

如需必要和選擇性關聯性之間的差異，請參閱[必要和選擇性關聯](#required-and-optional-relationships)性一節。

如需不同刪除行為和慣例所使用之預設值的詳細資訊，請參閱[Cascade Delete](../saving/cascade-delete.md) 。

## <a name="data-annotations"></a>資料註釋

有兩個可用來設定關聯`[ForeignKey]`性的資料批註：和。 `[InverseProperty]` 這些可在`System.ComponentModel.DataAnnotations.Schema`命名空間中取得。

### <a name="foreignkey"></a>[ForeignKey]

您可以使用資料批註來設定應該使用哪一個屬性做為指定關聯性的外鍵屬性。 這通常是在不依照慣例探索外鍵屬性時進行。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Relationships/ForeignKey.cs?highlight=30)]

> [!TIP]  
> `[ForeignKey]`批註可以放在關聯性中的任一導覽屬性上。 不需要移至相依實體類別中的導覽屬性。

### <a name="inverseproperty"></a>[InverseProperty]

您可以使用資料批註來設定相依和主體實體上的導覽屬性如何配對。 當兩個實體類型之間有一對以上的導覽屬性時，通常就會執行這項作業。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Relationships/InverseProperty.cs?highlight=33,36)]

## <a name="fluent-api"></a>Fluent API

若要在流暢的 API 中設定關聯性，您一開始會先識別構成關聯性的導覽屬性。 `HasOne`或者`HasMany` ，在您開始設定的實體類型上，識別導覽屬性。 接著，您可以將呼叫`WithOne`連結`WithMany`至或，以識別反向導覽。 `HasOne`/`WithOne`用於參考導覽屬性，並`HasMany` / `WithMany`用於集合導覽屬性。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NoForeignKey.cs?highlight=14-16)]

### <a name="single-navigation-property"></a>單一導覽屬性

如果您只有一個導覽屬性，則會有和`WithOne` `WithMany`的無參數多載。 這表示概念上的另一端有參考或集合，但實體類別中沒有包含任何導覽屬性。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/OneNavigation.cs?highlight=14-16)]

### <a name="foreign-key"></a>外鍵

您可以使用流暢的 API 來設定應該使用哪一個屬性做為指定之關聯性的外鍵屬性。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ForeignKey.cs?highlight=17)]

下列程式代碼清單顯示如何設定複合外鍵。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CompositeForeignKey.cs?highlight=20)]

您可以使用的字串`HasForeignKey(...)`多載，將陰影屬性設定為外鍵（如需詳細資訊，請參閱[陰影屬性](shadow-properties.md)）。 我們建議您先將 shadow 屬性明確加入至模型，再將其當做外鍵使用（如下所示）。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ShadowForeignKey.cs#Sample)]

### <a name="without-navigation-property"></a>沒有導覽屬性

您不一定需要提供導覽屬性。 您可以直接在關聯性的一端提供外鍵。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NoNavigation.cs?highlight=14-17)]

### <a name="principal-key"></a>主要金鑰

如果您想要外鍵參考主鍵以外的屬性，您可以使用流暢的 API 來設定關聯性的主體索引鍵屬性。 您設定為主體金鑰的屬性會自動設定為替代金鑰（如需詳細資訊，請參閱[替代金鑰](alternate-keys.md)）。

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Relationships/PrincipalKey.cs?highlight=11)] -->
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

下列程式代碼清單顯示如何設定複合主體索引鍵。

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Relationships/CompositePrincipalKey.cs?highlight=11)] -->
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
> 指定主體索引鍵屬性的順序必須符合為外鍵指定的順序。

### <a name="required-and-optional-relationships"></a>必要和選擇性的關聯性

您可以使用流暢的 API 來設定關聯性是否為必要或選擇性。 最後，這會控制外鍵屬性是否為必要或選擇性。 當您使用陰影狀態外鍵時，這是最有用的。 如果您的實體類別中有外鍵屬性，則關聯性的必要性取決於外鍵屬性是否為必要或選擇性（如需詳細資訊，請參閱[必要和選擇性屬性](required-optional.md)）。

<!-- [!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/Required.cs?highlight=11)] -->
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

您可以使用流暢的 API，為指定的關聯性明確設定串聯刪除行為。

如需每個選項的詳細討論，請參閱儲存資料的重迭[刪除](../saving/cascade-delete.md)一節。

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Relationships/CascadeDelete.cs?highlight=11)] -->
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

一對一關聯性在兩端都有參考導覽屬性。 它們遵循與一對多關聯性相同的慣例，但是在外鍵屬性上引進了唯一的索引，以確保只有一個相依性與每個主體相關。

<!-- [!code-csharp[Main](samples/core/Modeling/Conventions/Relationships/OneToOne.cs?highlight=6,15,16)] -->
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
> EF 會根據其偵測外鍵屬性的能力，選擇要相依的其中一個實體。 如果選擇錯誤的實體做為相依專案，您可以使用流暢的 API 來修正此問題。

設定與流暢 API 的關聯性時，您會使用`HasOne`和`WithOne`方法。

設定外鍵時，您需要指定相依實體類型-請注意下列清單中提供給`HasForeignKey`的泛型參數。 在一對多關聯性中，明確地指出具有參考導覽的實體是相依的，而具有該集合的實體是主體。 但這並不是一對一關聯性，因此需要明確定義。

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Relationships/OneToOne.cs?highlight=11)] -->
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

尚未支援多對多關聯性，但沒有實體類別來表示聯結資料表。 不過，您可以藉由包含聯結資料表的實體類別並對應兩個不同的一對多關聯性，來代表多對多關聯性。

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Relationships/ManyToMany.cs?highlight=11,12,13,14,16,17,18,19,39,40,41,42,43,44,45,46)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Post> Posts { get; set; }
    public DbSet<Tag> Tags { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<PostTag>()
            .HasKey(pt => new { pt.PostId, pt.TagId });

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
