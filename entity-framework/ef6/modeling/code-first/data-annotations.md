---
title: First 資料註解-EF6 的程式碼
author: divega
ms.date: 10/23/2016
ms.assetid: 80abefbd-23c9-4fce-9cd3-520e5df9856e
ms.openlocfilehash: 8d85ef85f56a23d9b3b526554417dc9dd360e139
ms.sourcegitcommit: 39080d38e1adea90db741257e60dc0e7ed08aa82
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2018
ms.locfileid: "50980037"
---
# <a name="code-first-data-annotations"></a>Code First 資料註解
> [!NOTE]
> **EF4.1 及更新版本僅**-功能、 Api、 Entity Framework 4.1 中導入等本頁所述。 如果您使用較早版本，部分或所有的這項資訊不適用於。

此頁面上的內容是來自原始寫入，作者： Julie Lerman 的發行項 (\<http://thedatafarm.com>)。

Entity Framework Code First 可讓您使用您自己的網域類別，代表模型執行查詢時，EF 相依於變更追蹤，以及更新函式。 程式碼首先會利用程式設計的模式稱為 'convention over configuration'。 程式碼第一次會假設您的類別遵循的慣例，Entity framework，並且在此情況下，便會自動生效時如何執行的作業。 不過，如果您的類別並不遵守這些慣例，您能夠將設定新增至您的類別，為 EF 提供的必要資訊。

程式碼首先提供兩種方式可將這些設定新增至您的類別。 其中一個使用簡單的屬性，稱為 DataAnnotations，且第二個使用 Code First 的 Fluent API，可讓您在程式碼中，以命令方式描述設定。

這篇文章將著重在使用 DataAnnotations （在 System.ComponentModel.DataAnnotations 命名空間中），若要設定您的類別 – 反白顯示最常需要的組態。 DataAnnotations 也會辨識由數項.NET 應用程式，例如 ASP.NET MVC 可讓這些應用程式運用相同的註釋的用戶端驗證。


## <a name="the-model"></a>模型

我將示範如何使用一對簡單類別的程式碼第一次 DataAnnotations： 部落格和文章。

``` csharp
    public class Blog
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string BloggerName { get; set;}
        public virtual ICollection<Post> Posts { get; set; }
    }

    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public DateTime DateCreated { get; set; }
        public string Content { get; set; }
        public int BlogId { get; set; }
        public ICollection<Comment> Comments { get; set; }
    }
```

如有需要，部落格和後置類別方便遵循程式碼的第一個慣例，且需要未調整，若要啟用 EF 的相容性。 不過，您也可以使用註解提供給 EF 的詳細資訊，相關的類別和它們所對應的資料庫。

 

## <a name="key"></a>Key

Entity Framework 依賴具有索引鍵的值會用於追蹤的實體，每個實體。 Code First 一個慣例是隱含的索引鍵屬性;第一次程式碼會尋找名為 「 識別碼 」 或類別名稱和 「 識別碼 」，例如"BlogId 」 的組合的屬性。 這個屬性會對應至資料庫中的主索引鍵資料行。

部落格和後置類別都遵循這個慣例。 如果它們沒有嗎？ 如果部落格已使用名稱*PrimaryTrackingKey*相反的或甚至*foo*嗎？ 如果程式碼第一次找不到符合此慣例的屬性則會因為 Entity Framework 的需求，您必須有索引鍵屬性來擲回例外狀況。 您可以使用索引鍵的註解來指定要用作 EntityKey 的哪一個屬性。

``` csharp
    public class Blog
    {
        [Key]
        public int PrimaryTrackingKey { get; set; }
        public string Title { get; set; }
        public string BloggerName { get; set;}
        public virtual ICollection<Post> Posts { get; set; }
    }
```

如果您是第一次使用程式碼是資料庫產生功能、 部落格資料表擁有名為 PrimaryTrackingKey，預設也定義為 Identity 的主索引鍵資料行。

![部落格資料表具有主索引鍵](~/ef6/media/jj591583-figure01.png)

### <a name="composite-keys"></a>複合索引鍵

Entity Framework 支援複合索引鍵-多個屬性所組成的主索引鍵。 例如，您可能有其主索引鍵是 PassportNumber 和組合 IssuingCountry Passport 類別。

``` csharp
    public class Passport
    {
        [Key]
        public int PassportNumber { get; set; }
        [Key]
        public string IssuingCountry { get; set; }
        public DateTime Issued { get; set; }
        public DateTime Expires { get; set; }
    }
```

嘗試在您的 EF 模型中使用上述的類別會導致`InvalidOperationException`:

*無法判斷複合主索引鍵排序類型 'Passport'。使用 ColumnAttribute 或 HasKey 方法來指定複合主索引鍵的順序。*

若要使用複合索引鍵，Entity Framework 會要求您定義的順序索引鍵的屬性。 您可以使用資料行註解，以指定的順序來執行這項操作。

>[!NOTE]
> 將順序值是相對的 （而非以索引為基礎） 讓您可以使用任何值。 例如，100 和 200 可接受來取代 1 和 2。

``` csharp
    public class Passport
    {
        [Key]
        [Column(Order=1)]
        public int PassportNumber { get; set; }
        [Key]
        [Column(Order = 2)]
        public string IssuingCountry { get; set; }
        public DateTime Issued { get; set; }
        public DateTime Expires { get; set; }
    }
```

如果您有複合外部索引鍵的實體，您必須指定相同的資料行排序，用於對應的主索引鍵屬性。

只有的相對順序內外部索引鍵屬性必須是相同的實際的值指派給**順序**不需要比對。 例如，在下列類別中，3 和 4 可能可用來取代 1 和 2。

``` csharp
    public class PassportStamp
    {
        [Key]
        public int StampId { get; set; }
        public DateTime Stamped { get; set; }
        public string StampingCountry { get; set; }

        [ForeignKey("Passport")]
        [Column(Order = 1)]
        public int PassportNumber { get; set; }

        [ForeignKey("Passport")]
        [Column(Order = 2)]
        public string IssuingCountry { get; set; }

        public Passport Passport { get; set; }
    }
```

## <a name="required"></a>必要

必要的註釋會告知 EF 所需之特定的屬性。

加入所需的 Title 屬性會強制 EF （和 MVC） 以確保屬性中沒有資料。

``` csharp
    [Required]
    public string Title { get; set; }
```

任何其他含應用程式中的程式碼或標記變更，在 MVC 應用程式將會執行用戶端驗證，甚至以動態方式建置訊息使用的屬性和註釋名稱。

![建立頁面，其標題是必要的錯誤](~/ef6/media/jj591583-figure02.png)

必要的屬性也會影響所產生的資料庫，藉由對應的內容不可為 null。 請注意，[標題] 欄位已變更為"not null"。

>[!NOTE]
> 在某些情況下它可能無法在資料庫中是不可為 null，即使需要此屬性，資料行。 比方說，當使用 TPH 繼承策略資料的多個類型會儲存在單一資料表。 如果衍生型別包含必要的屬性資料行無法進行不可為 null 因為並非所有的型別階層架構中會有這個屬性。

 

![部落格資料表](~/ef6/media/jj591583-figure03.png)

 

## <a name="maxlength-and-minlength"></a>MaxLength 與 MinLength

MaxLength 與 MinLength 屬性可讓您指定額外的屬性驗證，就像您一樣需要。

以下是 BloggerName 長度需求。 此範例也示範如何結合屬性。

``` csharp
    [MaxLength(10),MinLength(5)]
    public string BloggerName { get; set; }
```

MaxLength 註解會影響資料庫，藉由設定屬性的長度為 10。

![顯示最大長度 BloggerName 資料行上的部落格資料表](~/ef6/media/jj591583-figure04.png)

MVC 用戶端註釋和 EF 4.1 伺服器端註解將會同時採用這項驗證，再以動態方式建立一則錯誤訊息: 「 BloggerName 欄位必須是字串或陣列類型最大長度為 '10'。 」該訊息是有點長。 許多註釋可讓您與 ErrorMessage 屬性中指定的錯誤訊息。

``` csharp
    [MaxLength(10, ErrorMessage="BloggerName must be 10 characters or less"),MinLength(5)]
    public string BloggerName { get; set; }
```

您也可以指定錯誤訊息所需的註解中。

![建立具有自訂錯誤訊息的網頁](~/ef6/media/jj591583-figure05.png)

 

## <a name="notmapped"></a>NotMapped

程式碼的第一個慣例會要求每個支援的資料類型的屬性會以資料庫中。 但這不一定在您的應用程式。 例如，您可能會建立標題和 BloggerName 欄位為基礎的程式碼的部落格類別中有屬性。 該屬性會自動建立，而且不需要儲存。 您可以將不會對應至 NotMapped 註釋，例如此 BlogCode 屬性具有任何屬性的標記。

``` csharp
    [NotMapped]
    public string BlogCode
    {
        get
        {
            return Title.Substring(0, 1) + ":" + BloggerName.Substring(0, 1);
        }
    }
```

 

## <a name="complextype"></a>ComplexType

不常見描述您的領域實體透過一組類別，然後層這些類別來描述完整的實體。 比方說，您可能會新增至您的模型稱為 BlogDetails 的類別。

``` csharp
    public class BlogDetails
    {
        public DateTime? DateCreated { get; set; }

        [MaxLength(250)]
        public string Description { get; set; }
    }
```

請注意 BlogDetails 沒有任何類型的索引鍵屬性。 在 網域導向設計，BlogDetails 被指值的物件。 Entity Framework 會將稱為複雜類型的值物件。  複雜型別無法自行追蹤。

不過部落格類別，它將會追蹤部落格物件的一部分的 BlogDetails 中的屬性。 為了讓第一次將此辨識的程式碼，您必須將 BlogDetails 類別標示為 ComplexType。

``` csharp
    [ComplexType]
    public class BlogDetails
    {
        public DateTime? DateCreated { get; set; }

        [MaxLength(250)]
        public string Description { get; set; }
    }
```

現在您可以新增屬性來表示該部落格 BlogDetails 部落格類別中。

``` csharp
        public BlogDetails BlogDetail { get; set; }
```

在資料庫中，部落格資料表將包含的所有部落格，包括其 BlogDetail; 屬性所包含的屬性的屬性。 根據預設，每一個前面加上複雜類型，也就是 BlogDetail 的名稱。

![具有複雜類型的部落格資料表](~/ef6/media/jj591583-figure06.png)

另一個有趣的註解是，雖然 DateCreated 屬性定義為不可為 null 的日期時間，在類別中，相關的資料庫欄位是可為 null。 如果您想要影響的資料庫結構描述，您必須使用必要的註解。

 

## <a name="concurrencycheck"></a>ConcurrencyCheck

ConcurrencyCheck 註釋可讓您用於並行存取檢查資料庫中，當使用者編輯或刪除實體的一或多個屬性的旗標。 如果您使用的 EF 設計工具，這會與屬性的 ConcurrencyMode 設定為 已修正。

我們來看看 ConcurrencyCheck 藉由將 BloggerName 屬性的運作方式。

``` csharp
    [ConcurrencyCheck, MaxLength(10, ErrorMessage="BloggerName must be 10 characters or less"),MinLength(5)]
    public string BloggerName { get; set; }
```

當呼叫 SaveChanges 時，由於 ConcurrencyCheck 上的註解 BloggerName 欄位中，該屬性的原始值將用於更新。 此命令會嘗試找出正確的資料列篩選的索引鍵的值不僅在 BloggerName 的原始值。  以下是 傳送到資料庫的更新命令的重要部分，其中您可以看到此命令會更新資料列具有 PrimaryTrackingKey 是 1 到"Julie"，也就是原始的值，該部落格已從資料庫擷取時的 BloggerName。

``` SQL
    where (([PrimaryTrackingKey] = @4) and ([BloggerName] = @5))
    @4=1,@5=N'Julie'
```

如果有人變更過該部落格部落客名稱在此同時，這項更新將會失敗，您會收到要處理 DbUpdateConcurrencyException。

 

## <a name="timestamp"></a>時間戳記

就較常使用的並行存取檢查的 rowversion 或時間戳記欄位。 但是，而不是使用 ConcurrencyCheck 註釋，您可以使用更特定的時間戳記註解，只要將屬性的型別是位元組陣列。 程式碼第一次將時間戳記屬性視為相同 ConcurrencyCheck 屬性，但它也會確保程式碼第一次會產生的資料庫欄位是不可為 null。 您只能有一個時間戳記屬性中指定的類別。

部落格類別中加入下列屬性：

``` csharp
    [Timestamp]
    public Byte[] TimeStamp { get; set; }
```

在第一次建立資料庫資料表中的 不可為 null 的時間戳記資料行的程式碼中的結果。

![部落格與時間戳記資料行的資料表](~/ef6/media/jj591583-figure07.png)

 

## <a name="table-and-column"></a>資料表和資料行

如果您讓 Code First 建立的資料庫，您可能想要變更的資料表和其建立的資料行的名稱。 您也可以使用 Code First 與現有的資料庫。 但它不是一定的類別和您的網域中的屬性名稱比對的資料表和資料庫中的資料行的名稱。

我的類別名為部落格，並依照慣例，程式碼先假設這會對應到名為部落格的資料表。 如果不是您可以指定資料表的名稱與資料表屬性。 此處，例如註解會指定資料表名稱 InternalBlogs。

``` csharp
    [Table("InternalBlogs")]
    public class Blog
```

資料行註解是更多的精英中指定的對應資料行的屬性。 您可以規定名稱、 資料型別或甚至是將資料行出現在資料表中的順序。 以下是資料行屬性的範例。

``` csharp
    [Column("BlogDescription", TypeName="ntext")]
    public String Description {get;set;}
```

請勿混淆資料行的資料型別 DataAnnotation TypeName 屬性。 資料型別是用於 UI 的註釋，而且會忽略的 Code First。

已重新產生之後，以下是資料表。 資料表名稱已變更為 InternalBlogs，描述複雜型別資料行現在已 BlogDescription。 因為註解中指定的名稱，程式碼第一次不會使用資料行名稱開頭為複雜型別名稱的慣例。

![部落格資料表和資料行重新命名](~/ef6/media/jj591583-figure08.png)

 

## <a name="databasegenerated"></a>DatabaseGenerated

重要的資料庫功能是能夠有計算屬性。 如果您對應 Code First 類別，藉此資料表包含計算資料行，您不想要嘗試更新這些資料行的 Entity Framework。 但您想 EF 來插入或更新的資料之後，從資料庫傳回這些值。 您可以使用 DatabaseGenerated 註解來標示那些屬性，在您的類別，以及計算列舉。 其他列舉都是無和身分識別。

``` csharp
    [DatabaseGenerated(DatabaseGeneratedOption.Computed)]
    public DateTime DateCreated { get; set; }
```

您可以使用程式碼第一次產生資料庫時，產生位元組或時間戳記資料行上的資料庫，否則您應該只使用這個選項指向現有的資料庫，因為程式碼第一次將無法判斷計算資料行的公式。

您閱讀上述，根據預設，是一個整數的索引鍵屬性，將會成為在資料庫中的識別索引鍵。 這會是 DatabaseGenerated 設 DatabaseGeneratedOption.Identity 相同。 如果您不想要識別索引鍵，值可以設 DatabaseGeneratedOption.None。

 

## <a name="index"></a>索引

> [!NOTE]
> **EF6.1 及更新版本僅**-Entity Framework 6.1 中導入的索引屬性。 如果您使用較早版本不適用這一節的資訊。

您可以使用的一或多個資料行上建立索引**IndexAttribute**。 將屬性加入至一個或多個屬性將會導致 EF 在資料庫中建立對應的索引，建立資料庫時，或建立對應的結構**CreateIndex**呼叫如果您使用 Code First 移轉。

例如，下列程式碼會導致上所建立的索引**分級**資料行**文章**資料庫資料表中的。

``` csharp
    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }
        [Index]
        public int Rating { get; set; }
        public int BlogId { get; set; }
    }
```

根據預設，將會命名為索引**IX\_&lt;屬性名稱&gt;** (IX\_評等在上述範例中)。 您也可以透過指定索引的名稱。 下列範例會指定應該命名為索引**PostRatingIndex**。

``` csharp
    [Index("PostRatingIndex")]
    public int Rating { get; set; }
```

根據預設，索引為非唯一的但您可以使用**IsUnique**具名參數來指定應該是唯一的索引。 下列範例會介紹一個唯一的索引上**使用者**的登入名稱。

``` csharp
    public class User
    {
        public int UserId { get; set; }

        [Index(IsUnique = true)]
        [StringLength(200)]
        public string Username { get; set; }

        public string DisplayName { get; set; }
    }
```

### <a name="multiple-column-indexes"></a>多個資料行索引

跨越多個資料行的索引已指定在多個索引註解中使用相同的名稱，指定的資料表。 當您建立多重資料行索引時，您必須指定在索引中的資料行的順序。 比方說，下列程式碼會建立多重資料行索引上**分級**並**BlogId**呼叫**IX\_BlogAndRating**。 **BlogId**是在索引中的第一個資料行和**分級**是第二個。

``` csharp
    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }
        [Index("IX_BlogIdAndRating", 2)]
        public int Rating { get; set; }
        [Index("IX_BlogIdAndRating", 1)]
        public int BlogId { get; set; }
    }
```

 

## <a name="relationship-attributes-inverseproperty-and-foreignkey"></a>屬性關聯性： InverseProperty 和 ForeignKey

> [!NOTE]
> 此頁面提供有關設定 Code First 模型使用資料註解中的關聯性資訊。 如需 EF 以及如何存取和操作資料使用關聯性的關聯性的一般資訊，請參閱[關聯性和導覽屬性](~/ef6/fundamentals/relationships.md)。 *

程式碼的第一個慣例會負責在您的模型中，最常見的關聯性，但有某些情況下，它需要說明的位置。

變更建立問題貼文及其關聯性的部落格類別中的索引鍵屬性名稱。 

產生時的資料庫，程式碼第一次看到 BlogId 類別中的屬性後，並會將它，它符合類別名稱加上 「 識別碼 」，為部落格類別的外部索引鍵的慣例所辨識。 但是，部落格類別中沒有 BlogId 屬性。 這個解決方法是建立貼文中的導覽屬性，並使用外部索引 DataAnnotation 協助先了解如何建置這兩個類別之間的關聯性的程式碼 — 使用 Post.BlogId 屬性，以及如何指定條件約束中的資料庫。

``` csharp
    public class Post
    {
            public int Id { get; set; }
            public string Title { get; set; }
            public DateTime DateCreated { get; set; }
            public string Content { get; set; }
            public int BlogId { get; set; }
            [ForeignKey("BlogId")]
            public Blog Blog { get; set; }
            public ICollection<Comment> Comments { get; set; }
    }
```

在資料庫中的條件約束會顯示 InternalBlogs.PrimaryTrackingKey Posts.BlogId 之間的關聯性。 

![InternalBlogs.PrimaryTrackingKey 和 Posts.BlogId 之間的關聯性](~/ef6/media/jj591583-figure09.png)

當您有多個類別之間的關聯性時，會使用 InverseProperty。

在 Post 類別中，您可能想要追蹤是誰撰寫的部落格文章的以及誰可以編輯它。 以下是 Post 類別的兩個新導覽屬性。

``` csharp
    public Person CreatedBy { get; set; }
    public Person UpdatedBy { get; set; }
```

您也必須新增這些屬性所參考的 Person 類別中。 Person 類別有回到文章中，一個用於所有人員，另一個用於所有由該位人員更新的文章所撰寫的文章的導覽屬性。

``` csharp
    public class Person
    {
            public int Id { get; set; }
            public string Name { get; set; }
            public List<Post> PostsWritten { get; set; }
            public List<Post> PostsUpdated { get; set; }
    }
```

程式碼第一次不能符合其本身上的兩個類別中的屬性。 貼文的資料庫資料表應該有一個外部索引鍵的 CreatedBy 人員和另一個用於 UpdatedBy 人員但程式碼第一次會建立四個外部索引鍵屬性將會： 人\_識別碼、 人員\_Id1、 CreatedBy\_識別碼和UpdatedBy\_識別碼。

![張貼額外的外部索引鍵的資料表](~/ef6/media/jj591583-figure10.png)

若要修正這些問題，您可以使用 InverseProperty 註解來指定屬性的對齊方式。

``` csharp
    [InverseProperty("CreatedBy")]
    public List<Post> PostsWritten { get; set; }

    [InverseProperty("UpdatedBy")]
    public List<Post> PostsUpdated { get; set; }
```

親自轉交 PostsWritten 屬性會知道這是指 Post 型別，因為它會建置 Post.CreatedBy 的關聯性。 同樣地，PostsUpdated 將會連接到 Post.UpdatedBy。 程式碼第一次不會產生額外的外部索引鍵。

![貼文資料表，而不需要額外的外部索引鍵](~/ef6/media/jj591583-figure11.png)

 

## <a name="summary"></a>總結

DataAnnotations 不僅可讓您描述在您的程式碼的第一個類別，用戶端和伺服器端驗證，但它們也可讓您增強和甚至修正第一次程式碼可讓您根據其慣例的類別的相關假設。 使用 DataAnnotations 您可以不只有磁碟機產生資料庫結構描述，但您也可以將您的程式碼的第一個類別對應到預先存在的資料庫。

雖然它們是非常大的彈性，請記住，DataAnnotations 提供中最常需要組態變更，您可以對您的程式碼的第一個類別。 若要設定您的類別，針對某些邊緣案例，您應該查看的其他設定機制，Code First 的 Fluent API。
