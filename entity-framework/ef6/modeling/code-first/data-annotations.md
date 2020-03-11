---
title: Code First 資料批註-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 80abefbd-23c9-4fce-9cd3-520e5df9856e
ms.openlocfilehash: 9fac2a90c46d78ff5fd632800cc0050276467773
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78419182"
---
# <a name="code-first-data-annotations"></a>Code First 資料批註
> [!NOTE]
> **僅限 ef 4.1 [僅限**]-此頁面中所討論的功能、api 等已在 Entity Framework 4.1 中引進。 如果您使用的是較舊的版本，則不會套用本資訊的部分或全部。

此頁面上的內容可從原本由 Julie Lerman （\<http://thedatafarm.com>)所撰寫的文章進行調整。

Entity Framework Code First 可讓您使用自己的網域類別來代表 EF 依賴來執行查詢、變更追蹤和更新函數的模型。 Code First 利用稱為「設定的慣例」的程式設計模式。 Code First 會假設您的類別遵循 Entity Framework 的慣例，而在此情況下，將會自動處理如何執行其作業。 不過，如果您的類別未遵循這些慣例，您就能夠將設定新增至您的類別，以提供 EF 必要的資訊。

Code First 提供您兩種方式，將這些設定新增至您的類別。 其中一個是使用稱為 DataAnnotations 的簡單屬性，第二個則是使用 Code First 的流暢 API，這可讓您在程式碼中以命令方式描述設定。

本文將著重于使用 DataAnnotations （在 System.workflow.componentmodel.activity. DataAnnotations 命名空間中）來設定您的類別，並反白顯示最常需要的設定。 許多 .NET 應用程式（例如 ASP.NET MVC）也都能理解 DataAnnotations，這可讓這些應用程式利用相同的注釋來進行用戶端驗證。


## <a name="the-model"></a>模型

我將使用一組簡單的類別來示範 Code First DataAnnotations： Blog 和 Post。

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

就像是一樣，Blog 和 Post 類別可以方便遵循 code first 慣例，而且不需要調整就能啟用 EF 相容性。 不過，您也可以使用批註，將有關類別及其對應之資料庫的詳細資訊提供給 EF。

 

## <a name="key"></a>Key

Entity Framework 依賴每個實體具有用於實體追蹤的索引鍵值。 Code First 的其中一個慣例是隱含的索引鍵屬性;Code First 會尋找名為 "Id" 的屬性，或類別名稱和 "Id" 的組合，例如 "BlogId"。 這個屬性會對應至資料庫中的主鍵資料行。

[Blog] 和 [Post] 類別都遵循此慣例。 如果沒有的話，該怎麼辦？ 如果 Blog 使用名稱*PrimaryTrackingKey* ，或甚至是*foo*，該怎麼辦？ 如果 code first 找不到符合此慣例的屬性，就會擲回例外狀況，因為 Entity Framework 的需求必須要有索引鍵屬性。 您可以使用索引鍵注釋來指定要使用哪一個屬性做為 EntityKey。

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

如果您使用 code first 的資料庫產生功能，則 Blog 資料表會有一個名為 PrimaryTrackingKey 的主鍵資料行，其預設也會定義為身分識別。

![具有主要金鑰的 Blog 資料表](~/ef6/media/jj591583-figure01.png)

### <a name="composite-keys"></a>複合索引鍵

Entity Framework 支援複合索引鍵-由一個以上的屬性所組成的主要索引鍵。 例如，您可以擁有一個 Passport 類別，其主要金鑰是 PassportNumber 和 IssuingCountry 的組合。

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

嘗試在 EF 模型中使用上述類別會產生 `InvalidOperationException`：

*無法判斷類型 ' Passport ' 的複合主要索引鍵排序。使用 ColumnAttribute 或 HasKey 方法來指定複合主鍵的順序。*

為了使用複合索引鍵，Entity Framework 需要您定義索引鍵屬性的順序。 若要這麼做，您可以使用資料行批註來指定訂單。

>[!NOTE]
> 順序值是相對的（而不是以索引為基礎），因此可以使用任何值。 例如，100和200可以接受，以取代1和2。

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

如果您有具有複合外鍵的實體，則您必須指定用於對應主鍵屬性的相同資料行順序。

只有外鍵屬性中的相對順序必須相同，指派給**Order**的確切值不需要相符。 例如，在下列類別中，可以使用3和4來取代1和2。

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

必要的注釋會告訴 EF 需要特定屬性。

將 Required 加入 Title 屬性將會強制 EF （和 MVC），以確保屬性中有資料。

``` csharp
    [Required]
    public string Title { get; set; }
```

在應用程式中不會有任何額外的程式碼或標記變更，MVC 應用程式會執行用戶端驗證，甚至使用屬性和批註名稱動態建立訊息。

![需要標題為 [建立] 頁面的錯誤](~/ef6/media/jj591583-figure02.png)

必要的屬性也會影響所產生的資料庫，方法是讓對應的屬性成為不可為 null。 請注意，[標題] 欄位已變更為 [非 null]。

>[!NOTE]
> 在某些情況下，即使屬性是必要的，資料庫中的資料行還是無法是不可為 null 的。 例如，使用多個類型的 TPH 繼承策略資料時，會儲存在單一資料表中。 如果衍生類型包含必要的屬性，則資料行不能設為不可為 null，因為不是階層中的所有類型都有這個屬性。

 

![Blog 資料表](~/ef6/media/jj591583-figure03.png)

 

## <a name="maxlength-and-minlength"></a>MaxLength 和 MinLength

MaxLength 和 MinLength 屬性可讓您指定其他屬性驗證，就如同您在必要時所做的一樣。

以下是長度需求的 BloggerName。 此範例也會示範如何結合屬性。

``` csharp
    [MaxLength(10),MinLength(5)]
    public string BloggerName { get; set; }
```

MaxLength 注釋會藉由將屬性的長度設定為10來影響資料庫。

![顯示 BloggerName 資料行最大長度的 blog 資料表](~/ef6/media/jj591583-figure04.png)

MVC 用戶端注釋和 EF 4.1 伺服器端批註都會接受這項驗證，再次動態建立錯誤訊息：「欄位 BloggerName 必須是字串或陣列類型，最大長度為 ' 10 '。」該訊息有點長。 許多批註可讓您指定具有 ErrorMessage 屬性的錯誤訊息。

``` csharp
    [MaxLength(10, ErrorMessage="BloggerName must be 10 characters or less"),MinLength(5)]
    public string BloggerName { get; set; }
```

您也可以在必要的注釋中指定 ErrorMessage。

![建立包含自訂錯誤訊息的頁面](~/ef6/media/jj591583-figure05.png)

 

## <a name="notmapped"></a>NotMapped

Code first 慣例會指示每個支援資料類型的屬性都會在資料庫中表示。 但在您的應用程式中，這種情況不一定如此。 例如，您在 Blog 類別中的屬性可能會根據標題和 BloggerName 欄位建立程式碼。 該屬性可以動態建立，不需要儲存。 您可以使用 NotMapped 注釋（例如此 BlogCode 屬性），將未對應至資料庫的任何屬性標示為。

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

在一組類別中描述您的領域實體並不常見，然後將這些類別分層以描述完整的實體。 例如，您可以將名為 BlogDetails 的類別新增至您的模型。

``` csharp
    public class BlogDetails
    {
        public DateTime? DateCreated { get; set; }

        [MaxLength(250)]
        public string Description { get; set; }
    }
```

請注意，BlogDetails 沒有任何類型的索引鍵屬性。 在網域導向設計中，BlogDetails 稱為「值物件」。 Entity Framework 將值物件參考為複雜類型。  複雜類型無法自行追蹤。

不過，做為 Blog 類別中的屬性，BlogDetails 會被追蹤為 Blog 物件的一部分。 為了讓 code first 能夠辨識這一點，您必須將 BlogDetails 類別標記為 ComplexType。

``` csharp
    [ComplexType]
    public class BlogDetails
    {
        public DateTime? DateCreated { get; set; }

        [MaxLength(250)]
        public string Description { get; set; }
    }
```

現在您可以在 Blog 類別中新增屬性，以代表該 blog 的 BlogDetails。

``` csharp
        public BlogDetails BlogDetail { get; set; }
```

在資料庫中，Blog 資料表會包含 blog 的所有屬性，包括其 BlogDetail 屬性中包含的屬性。 根據預設，每個名稱的前面都會加上複雜型別 BlogDetail。

![具有複雜類型的 Blog 資料表](~/ef6/media/jj591583-figure06.png)


## <a name="concurrencycheck"></a>ConcurrencyCheck

ConcurrencyCheck 注釋可讓您在使用者編輯或刪除實體時，將一個或多個要用於資料庫並行檢查的屬性加上旗標。 如果您曾經使用過 EF Designer，這會與將屬性的 ConcurrencyMode 設定為 Fixed 一致。

讓我們來瞭解 ConcurrencyCheck 如何藉由將它新增至 BloggerName 屬性來運作。

``` csharp
    [ConcurrencyCheck, MaxLength(10, ErrorMessage="BloggerName must be 10 characters or less"),MinLength(5)]
    public string BloggerName { get; set; }
```

呼叫 SaveChanges 時，因為 BloggerName 欄位上的 ConcurrencyCheck 注釋，所以該屬性的原始值將會用於更新中。 此命令將不只會篩選索引鍵值，同時也會針對 BloggerName 的原始值，嘗試找出正確的資料列。  以下是傳送到資料庫之 UPDATE 命令的重要部分，您可以在其中看到此命令會更新具有 PrimaryTrackingKey 的資料列，以及 BloggerName 的 "Julie"，這是從資料庫抓取該 blog 時的原始值。

``` SQL
    where (([PrimaryTrackingKey] = @4) and ([BloggerName] = @5))
    @4=1,@5=N'Julie'
```

如果有人在此同時變更了該 blog 的博客名稱，這項更新將會失敗，而您將會收到您需要處理的 DbUpdateConcurrencyException。

 

## <a name="timestamp"></a>TimeStamp

使用 rowversion 或 timestamp 欄位進行並行檢查比較常見。 但是，您可以使用更特定的時間戳記注釋，而不是使用 ConcurrencyCheck 注釋，只要屬性的類型為位元組陣列即可。 Code first 會將 Timestamp 屬性視為與 ConcurrencyCheck 屬性相同，但它也會確保程式碼第一次產生的資料庫欄位不可為 null。 在指定的類別中，您只能有一個時間戳記屬性。

將下列屬性新增至 Blog 類別：

``` csharp
    [Timestamp]
    public Byte[] TimeStamp { get; set; }
```

在程式碼中，第一次在資料庫資料表中建立不可為 null 的 timestamp 資料行。

![包含時間戳記資料行的 blog 資料表](~/ef6/media/jj591583-figure07.png)

 

## <a name="table-and-column"></a>資料表和資料行

如果您要讓 Code First 建立資料庫，您可能會想要變更所建立之資料表和資料行的名稱。 您也可以使用 Code First 搭配現有的資料庫。 但是，您的網域中的類別和屬性名稱不一定會符合資料庫中的資料表和資料行名稱。

我的類別稱為 Blog，而 code first 會假設這會對應到名為 Blog 的資料表。 如果不是這種情況，您可以使用 Table 屬性來指定資料表的名稱。 例如，批註會指定資料表名稱是 InternalBlogs。

``` csharp
    [Table("InternalBlogs")]
    public class Blog
```

在指定對應資料行的屬性時，資料行注釋更為熟練。 您可以規定 [名稱]、[資料類型]，或甚至是資料表中顯示的資料行順序。 以下是資料行屬性的範例。

``` csharp
    [Column("BlogDescription", TypeName="ntext")]
    public String Description {get;set;}
```

請勿將資料行的 TypeName 屬性與 DataType DataAnnotation 混淆。 DataType 是用於 UI 的注釋，Code First 會予以忽略。

以下是重新產生的資料表。 資料表名稱已變更為 InternalBlogs，而複雜類型的 Description 資料行現在是 BlogDescription。 因為注釋中已指定名稱，所以 code first 不會使用以複雜型別名稱來啟動資料行名稱的慣例。

![已重新命名的 blog 資料表和資料行](~/ef6/media/jj591583-figure08.png)

 

## <a name="databasegenerated"></a>DatabaseGenerated

重要的資料庫功能是具有計算屬性的能力。 如果您要將 Code First 類別對應至包含計算資料行的資料表，您不會想要 Entity Framework 嘗試更新這些資料行。 但是，您可以在插入或更新資料之後，讓 EF 從資料庫傳回這些值。 您可以使用 DatabaseGenerated 注釋，將類別中的屬性和計算的列舉一併加上旗標。 其他列舉為 None 和 Identity。

``` csharp
    [DatabaseGenerated(DatabaseGeneratedOption.Computed)]
    public DateTime DateCreated { get; set; }
```

當程式碼第一次產生資料庫時，您可以使用在 byte 或 timestamp 資料行上產生的資料庫，否則，您應該只在指向現有的資料庫時才使用它，因為程式碼 first 無法判斷計算資料行的公式。

您會在上面閱讀，根據預設，做為整數的索引鍵屬性會成為資料庫中的識別索引鍵。 也就是將 DatabaseGenerated 設定為 DatabaseGeneratedOption。 如果您不想讓它成為身分識別索引鍵，您可以將值設定為 DatabaseGeneratedOption。

 

## <a name="index"></a>索引

> [!NOTE]
> **Ef 6.1 僅適用**于 Entity Framework 6.1 中引進的索引屬性。 如果您使用較舊的版本，本節中的資訊將不適用。

您可以使用**IndexAttribute**，在一或多個資料行上建立索引。 將屬性加入至一個或多個屬性，會使 EF 在建立資料庫時于資料庫中建立對應的索引，或 scaffold 對應的**CreateIndex**呼叫（如果您使用 Code First 移轉）。

例如，下列程式碼會在資料庫的 [**貼**文] 資料表的 [**評**等] 資料行上建立索引。

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

根據預設，索引會命名為**IX\_&lt;屬性名稱&gt;** （在上述範例中為 IX\_分級）。 不過，您也可以指定索引的名稱。 下列範例會指定索引應該命名為**PostRatingIndex**。

``` csharp
    [Index("PostRatingIndex")]
    public int Rating { get; set; }
```

根據預設，索引不是唯一的，但您可以使用**IsUnique**具名引數來指定索引應該是唯一的。 下列範例會在**使用者**的登入名稱上引進唯一索引。

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

### <a name="multiple-column-indexes"></a>多重資料行索引

跨越多個資料行的索引是使用相同的名稱，在指定資料表的多個索引注釋中指定。 當您建立多重資料行索引時，您必須指定索引中資料行的順序。 例如，下列程式碼會在**分級**和**BlogId**上建立多個資料行的索引，稱為**IX\_BlogIdAndRating**。 **BlogId**是索引中的第一個資料行，而**評**等是第二個數據行。

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

 

## <a name="relationship-attributes-inverseproperty-and-foreignkey"></a>關聯性屬性： InverseProperty 和 ForeignKey

> [!NOTE]
> 此頁面提供使用資料批註在 Code First 模型中設定關聯性的相關資訊。 如需 EF 中的關聯性以及如何使用關聯性存取和運算元據的一般資訊，請參閱[& 導覽屬性的關聯](~/ef6/fundamentals/relationships.md)性。 *

Code first 慣例會負責您模型中最常見的關聯性，但在某些情況下，它需要協助。

變更 Blog 類別中的索引鍵屬性名稱，會建立與 Post 關聯的問題。 

產生資料庫時，code first 會看到 Post 類別中的 BlogId 屬性，並依照其符合類別名稱加上 "Id" 的慣例來辨識它，作為 Blog 類別的外鍵。 但在 blog 類別中沒有 BlogId 屬性。 其解決方法是在 Post 中建立導覽屬性，並使用外部 DataAnnotation 協助程式碼先瞭解如何使用 BlogId 屬性來建立兩個類別之間的關聯性，以及如何在中指定條件約束。資料.

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

資料庫中的條件約束會顯示 InternalBlogs PrimaryTrackingKey 與 BlogId 之間的關聯性。 

![InternalBlogs 之間的關聯性 PrimaryTrackingKey 和貼文。 BlogId](~/ef6/media/jj591583-figure09.png)

當您的類別之間有多個關聯性時，就會使用 InverseProperty。

在 Post 類別中，您可能想要追蹤誰寫了 blog 文章以及編輯者。 以下是 Post 類別的兩個新導覽屬性。

``` csharp
    public Person CreatedBy { get; set; }
    public Person UpdatedBy { get; set; }
```

您也必須在這些屬性所參考的 Person 類別中加入。 Person 類別具有回到文章的導覽屬性，一個用於個人所撰寫的所有貼文，另一個則用於該人員所更新的所有文章。

``` csharp
    public class Person
    {
            public int Id { get; set; }
            public string Name { get; set; }
            public List<Post> PostsWritten { get; set; }
            public List<Post> PostsUpdated { get; set; }
    }
```

Code first 無法單獨比對這兩個類別中的屬性。 貼文的資料庫資料表應該有一個 CreatedBy 人員的外鍵，另一個用於 UpdatedBy 人員，但 code first 會建立四個外鍵屬性： Person\_Id、Person\_Id1、CreatedBy\_Id 和 UpdatedBy\_Id。

![附有額外外鍵的貼文資料表](~/ef6/media/jj591583-figure10.png)

若要修正這些問題，您可以使用 InverseProperty 批註來指定屬性的對齊方式。

``` csharp
    [InverseProperty("CreatedBy")]
    public List<Post> PostsWritten { get; set; }

    [InverseProperty("UpdatedBy")]
    public List<Post> PostsUpdated { get; set; }
```

因為 Person 中的 PostsWritten 屬性知道這會參考 Post 類型，所以它會建立 CreatedBy 的關聯性。 同樣地，PostsUpdated 會連接到 UpdatedBy。 而 code first 不會建立額外的外鍵。

![沒有額外外鍵的貼文資料表](~/ef6/media/jj591583-figure11.png)

 

## <a name="summary"></a>摘要

DataAnnotations 不僅可讓您在程式碼的第一個類別中描述用戶端和伺服器端驗證，還可讓您增強，甚至更正程式碼第一次將根據其慣例來對類別進行的假設。 有了 DataAnnotations，您不僅可以驅動資料庫架構的產生，還可以將程式碼的第一個類別對應到既有的資料庫。

雖然它們非常有彈性，但請記住，DataAnnotations 只會提供您在程式碼第一個類別上可以進行的最常需要的設定變更。 若要針對某些邊緣案例設定您的類別，您應該查看其他設定機制，Code First 的流暢 API。
