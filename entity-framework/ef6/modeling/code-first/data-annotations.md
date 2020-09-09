---
title: Code First 資料批註-EF6
description: Entity Framework 6 中的 Code First 資料批註
author: divega
ms.date: 10/23/2016
ms.assetid: 80abefbd-23c9-4fce-9cd3-520e5df9856e
uid: ef6/modeling/code-first/data-annotations
ms.openlocfilehash: 37b2872b5fb40cf0dc346554b2cbb9e3fffd6bdc
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617354"
---
# <a name="code-first-data-annotations"></a>Code First 資料註解
> [!NOTE]
> **僅限 ef 4.1** ：此頁面中所討論的功能、api 等已于 Entity Framework 4.1 中引進。 如果您使用的是較舊的版本，則不適用部分或全部資訊。

此頁面上的內容是由 Julie Lerman () 最初撰寫的文章所調整 \<http://thedatafarm.com> 。

Entity Framework Code First 可讓您使用自己的網域類別來代表 EF 依賴來執行查詢、變更追蹤和更新函數的模型。 Code First 會利用稱為「慣例 over 設定」的程式設計模式。 Code First 將假設您的類別遵循 Entity Framework 的慣例，在這種情況下，將會自動瞭解如何執行其工作。 但是，如果您的類別未遵循這些慣例，您可以將設定新增至您的類別，以提供 EF 具有必要的資訊。

Code First 提供兩種方式，將這些設定新增至您的類別。 其中一個是使用簡單的屬性（稱為 DataAnnotations），第二個則是使用 Code First 的流暢 API，可讓您以命令方式在程式碼中描述設定。

本文將著重于使用 DataAnnotations 命名空間中的 DataAnnotations () 設定您的類別–強調最常需要的設定。 許多 .NET 應用程式（例如 ASP.NET MVC）也會瞭解 DataAnnotations，這類應用程式可讓這些應用程式利用相同的注釋來進行用戶端驗證。


## <a name="the-model"></a>模型

我將示範如何使用一組簡單的類別 Code First DataAnnotations： Blog 和 Post。

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

如同這些，Blog 和 Post 類別可方便您遵循 code first 慣例，而不需要調整以啟用 EF 相容性。 不過，您也可以使用注釋，將有關類別和其所對應之資料庫的詳細資訊提供給 EF。

 

## <a name="key"></a>答案

Entity Framework 依賴每個實體具有用於實體追蹤的索引鍵值。 Code First 的其中一個慣例是隱含索引鍵屬性;Code First 會尋找名為 "Id" 的屬性，或類別名稱和 "Id" 的組合，例如 "BlogId"。 這個屬性會對應至資料庫中的主鍵資料行。

Blog 和 Post 類別都遵循此慣例。 如果沒有，該怎麼辦？ 如果 Blog 使用名稱 *PrimaryTrackingKey* ，或甚至是 *foo*？ 如果 code first 找不到符合這個慣例的屬性，就會擲回例外狀況，因為 Entity Framework 必須有索引鍵屬性的需求。 您可以使用索引鍵批註來指定要使用哪個屬性作為 EntityKey。

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

如果您使用 code first 的資料庫產生功能，則 Blog 資料表將會有一個名為 PrimaryTrackingKey 的主鍵資料行，此資料行也預設定義為身分識別。

![具有主鍵的 Blog 資料表](~/ef6/media/jj591583-figure01.png)

### <a name="composite-keys"></a>複合索引鍵

Entity Framework 支援複合索引鍵-包含一個以上屬性的主鍵。 例如，您可能有一個 Passport 類別，其主鍵是 PassportNumber 和 IssuingCountry 的組合。

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

嘗試在 EF 模型中使用上述類別會導致 `InvalidOperationException` ：

*無法判斷類型 ' Passport ' 的複合主要索引鍵順序。使用 ColumnAttribute 或 HasKey 方法來指定複合主鍵的順序。*

若要使用複合索引鍵，Entity Framework 必須定義索引鍵屬性的順序。 您可以使用資料行批註來指定順序，來進行這項作業。

>[!NOTE]
> 順序值是相對 (而不是以索引為基礎) 因此可以使用任何值。 例如，可以接受100和200來取代1和2。

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

如果您有具有複合外鍵的實體，則必須指定用於對應主鍵屬性的相同資料行排序。

只有外鍵屬性內的相對順序必須相同，指派給 **Order** 的確切值不需要相符。 例如，在下列類別中，3和4可以用來取代1和2。

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

`Required`批註會告知 EF 需要特定的屬性。

將必要專案加入 Title 屬性會強制 EF (和 MVC) ，以確保屬性中有資料。

``` csharp
    [Required]
    public string Title { get; set; }
```

在應用程式中沒有任何額外的程式碼或標記變更時，MVC 應用程式會執行用戶端驗證，甚至是使用屬性和批註名稱動態建立訊息。

![具有標題的建立頁面必須有錯誤](~/ef6/media/jj591583-figure02.png)

必要屬性也會將對應的屬性設為不可為 null，進而影響產生的資料庫。 請注意，[標題] 欄位已變更為 [不是 null]。

>[!NOTE]
> 在某些情況下，即使屬性是必要的，資料庫中的資料行還是可能無法成為可為 null。 例如，使用多個類型的 TPH 繼承策略資料時，會儲存在單一資料表中。 如果衍生型別包含必要的屬性，則資料行不能成為不可為 null，因為階層中的所有類型都不會有這個屬性。

 

![Blog 資料表](~/ef6/media/jj591583-figure03.png)

 

## <a name="maxlength-and-minlength"></a>MaxLength 和 MinLength

`MaxLength`和 `MinLength` 屬性可讓您指定其他屬性驗證，就像您使用的一樣 `Required` 。

以下是長度需求的 BloggerName。 此範例也會示範如何結合屬性。

``` csharp
    [MaxLength(10),MinLength(5)]
    public string BloggerName { get; set; }
```

MaxLength 注釋會將屬性的長度設定為10，以影響資料庫。

![顯示 BloggerName 資料行最大長度的 blog 資料表](~/ef6/media/jj591583-figure04.png)

MVC 用戶端注釋和 EF 4.1 伺服器端注釋都會接受這項驗證，再次動態建立錯誤訊息：「欄位 BloggerName 必須是最大長度為 ' 10 ' 的字串或陣列類型。」該訊息很長。 許多批註可讓您使用 ErrorMessage 屬性來指定錯誤訊息。

``` csharp
    [MaxLength(10, ErrorMessage="BloggerName must be 10 characters or less"),MinLength(5)]
    public string BloggerName { get; set; }
```

您也可以在必要的批註中指定 ErrorMessage。

![具有自訂錯誤訊息的 [建立] 頁面](~/ef6/media/jj591583-figure05.png)

 

## <a name="notmapped"></a>NotMapped

Code first 慣例規定在資料庫中表示支援的資料類型的每個屬性。 但在您的應用程式中，這不一定是如此。 例如，您的 Blog 類別中可能會有一個以標題和 BloggerName 欄位為基礎建立程式碼的屬性。 該屬性可以動態建立，且不需要儲存。 您可以使用 NotMapped 批註（例如這個 BlogCode 屬性），將任何未對應到資料庫的屬性標示為。

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

在一組類別之間描述您的領域實體，然後將這些類別分層以描述完整的實體並不常見。 例如，您可以將名為 BlogDetails 的類別新增至您的模型。

``` csharp
    public class BlogDetails
    {
        public DateTime? DateCreated { get; set; }

        [MaxLength(250)]
        public string Description { get; set; }
    }
```

請注意，沒有 `BlogDetails` 任何類型的索引鍵屬性。 在網域導向設計中， `BlogDetails` 稱為值物件。 Entity Framework 將值物件參考為複雜類型。複雜類型無法自行追蹤。

但是，做為類別中的屬性時 `Blog` ， `BlogDetails` 將會追蹤為物件的一部分 `Blog` 。 為了讓 code first 辨識這一點，您必須將類別標記 `BlogDetails` 為 `ComplexType` 。

``` csharp
    [ComplexType]
    public class BlogDetails
    {
        public DateTime? DateCreated { get; set; }

        [MaxLength(250)]
        public string Description { get; set; }
    }
```

現在您可以在類別中加入屬性， `Blog` 以代表 `BlogDetails` 該 blog 的。

``` csharp
        public BlogDetails BlogDetail { get; set; }
```

在資料庫中， `Blog` 資料表將會包含所有 blog 的屬性，包括其屬性中包含的屬性 `BlogDetail` 。 根據預設，每個名稱前面都會加上複雜型別 "BlogDetail" 的名稱。

![具有複雜類型的 Blog 資料表](~/ef6/media/jj591583-figure06.png)


## <a name="concurrencycheck"></a>ConcurrencyCheck

`ConcurrencyCheck`當使用者編輯或刪除實體時，批註可讓您將一或多個屬性標示為要用於資料庫中的平行存取檢查。 如果您使用的是 EF 設計工具，則會將屬性的設定 `ConcurrencyMode` 為 `Fixed` 。

讓我們來看看如何藉 `ConcurrencyCheck` 由將它新增至屬性來運作 `BloggerName` 。

``` csharp
    [ConcurrencyCheck, MaxLength(10, ErrorMessage="BloggerName must be 10 characters or less"),MinLength(5)]
    public string BloggerName { get; set; }
```

當 `SaveChanges` 呼叫時，因為 `ConcurrencyCheck` 欄位上的注釋 `BloggerName` ，所以會在更新中使用該屬性的原始值。 此命令會藉由篩選不只是在索引鍵值，而是在的原始值上，來嘗試找出正確的資料列 `BloggerName` 。以下是傳送至資料庫之 UPDATE 命令的重要部分，您可以在其中看到此命令會更新具有1的資料列 `PrimaryTrackingKey` 和 "Julie" 的資料列， `BloggerName` 這是從資料庫取出該 blog 的原始值。

``` SQL
    where (([PrimaryTrackingKey] = @4) and ([BloggerName] = @5))
    @4=1,@5=N'Julie'
```

如果有人在此同時變更了該 blog 的 blog 名稱，此更新將會失敗，而且您將會收到需要處理的 **DbUpdateConcurrencyException** 。

 

## <a name="timestamp"></a>TimeStamp

針對平行存取檢查使用 rowversion 或時間戳記欄位更為普遍。 但是 `ConcurrencyCheck` ， `TimeStamp` 只要屬性的類型是位元組陣列，您就可以使用較特定的注釋，而不是使用批註。 Code first 會將 `Timestamp` 屬性視為與 `ConcurrencyCheck` 屬性相同，但它也會確保程式碼第一個產生的資料庫欄位不是可為 null。 在指定的類別中，您只能有一個時間戳記屬性。

將下列屬性新增至 Blog 類別：

``` csharp
    [Timestamp]
    public Byte[] TimeStamp { get; set; }
```

導致程式碼先在資料庫資料表中建立不可為 null 的時間戳記資料行。

![具有時間戳記資料行的 blog 資料表](~/ef6/media/jj591583-figure07.png)

 

## <a name="table-and-column"></a>資料表和資料行

如果您讓 Code First 建立資料庫，您可能會想要變更其所建立之資料表和資料行的名稱。 您也可以搭配現有的資料庫使用 Code First。 但是，您的網域中的類別和屬性名稱不一定會符合資料庫中的資料表和資料行名稱。

我的類別命名為 `Blog` and by 慣例，code first 會假設這會對應到名為的資料表 `Blogs` 。 如果不是這種情況，您可以使用屬性來指定資料表的名稱 `Table` 。 例如，批註會指定資料表名稱是 **InternalBlogs**。

``` csharp
    [Table("InternalBlogs")]
    public class Blog
```

`Column`批註更能指定對應資料行的屬性。 您可以規定名稱、資料類型，甚至是資料行出現在資料表中的順序。 以下是屬性的範例 `Column` 。

``` csharp
    [Column("BlogDescription", TypeName="ntext")]
    public String Description {get;set;}
```

請勿 `TypeName` 將資料行的屬性與 DataType DataAnnotation 混淆。 DataType 是用於 UI 的注釋，Code First 會忽略此批註。

以下是重新產生資料表之後的資料表。 資料表名稱已變更為 **InternalBlogs** ，而 `Description` 複雜類型中的資料行現在已變更 `BlogDescription` 。 因為名稱是在批註中指定，所以 code first 不會使用以複雜型別名稱來啟動資料行名稱的慣例。

![重新命名的 blog 資料表和資料行](~/ef6/media/jj591583-figure08.png)

 

## <a name="databasegenerated"></a>DatabaseGenerated

重要的資料庫功能是具有計算屬性的能力。 如果您要將 Code First 類別對應至包含計算資料行的資料表，則不希望 Entity Framework 嘗試更新這些資料行。 但是，您希望 EF 在插入或更新資料之後，從資料庫傳回這些值。 您可以使用 `DatabaseGenerated` 批註來標記類別中的那些屬性以及 `Computed` 列舉。 其他列舉為 `None` 和 `Identity` 。

``` csharp
    [DatabaseGenerated(DatabaseGeneratedOption.Computed)]
    public DateTime DateCreated { get; set; }
```

當 code first 產生資料庫時，您可以使用在位元組或時間戳記資料行上產生的資料庫，否則您應該只在指向現有的資料庫時使用此資料庫，因為 code first 將無法判斷計算資料行的公式。

您會在上面閱讀過，預設為整數的索引鍵屬性會成為資料庫中的識別索引鍵。 這與設定相同 `DatabaseGenerated` `DatabaseGeneratedOption.Identity` 。 如果您不想讓它成為身分識別索引鍵，可以將值設定為 `DatabaseGeneratedOption.None` 。

 

## <a name="index"></a>索引

> [!NOTE]
> **僅限 ef 6.1** -此 `Index` 屬性是在 Entity Framework 6.1 中引進。 如果您使用的是較舊的版本，本節中的資訊並不適用。

您可以使用 **IndexAttribute**，在一個或多個資料行上建立索引。 將屬性加入至一個或多個屬性，將會導致 EF 在建立資料庫時于資料庫中建立對應的索引，如果您使用 Code First 移轉，則會 scaffold 對應的 **CreateIndex** 呼叫。

例如，下列程式碼會在 `Rating` 資料庫的資料表資料行上建立索引 `Posts` 。

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

根據預設，在上述範例中，索引將會命名為**ix \_ &lt; &gt; 屬性名稱** (ix \_ 分級) 。 不過，您也可以指定索引的名稱。 下列範例會指定索引應該命名為 `PostRatingIndex` 。

``` csharp
    [Index("PostRatingIndex")]
    public int Rating { get; set; }
```

根據預設，索引不是唯一的，但您可以使用 `IsUnique` 具名引數來指定索引應該是唯一的。 下列範例會在的登入名稱上引進唯一索引 `User` 。

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

跨多個資料行的索引是使用相同的名稱，在指定資料表的多個索引注釋中使用相同的名稱所指定。 當您建立多個資料行的索引時，您需要指定索引中資料行的順序。 例如，下列程式碼會在上建立多資料行索引 `Rating` ，並 `BlogId` 呼叫 **IX \_ BlogIdAndRating**。 `BlogId` 這是索引中的第一個資料行，而 `Rating` 是第二個數據行。

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
> 此頁面提供使用資料批註在 Code First 模型中設定關聯性的相關資訊。 如需 EF 中關聯性的一般資訊，以及如何使用關聯性存取和運算元據，請參閱 [關聯性 & 導覽屬性](xref:ef6/fundamentals/relationships)。 *

Code first 慣例將負責您模型中最常見的關聯性，但在某些情況下需要協助。

變更類別中的索引鍵屬性名稱時，會 `Blog` 產生與其關聯性的問題 `Post` 。 

產生資料庫時，code first 會看到 `BlogId` Post 類別中的屬性，並根據其符合類別名稱加上 **識別碼**的慣例加以辨識，以做為類別的外鍵 `Blog` 。 但在 `BlogId` blog 類別中沒有屬性。 的解決方法是在中建立導覽屬性 `Post` ，並使用 `ForeignKey` DataAnnotation 來協助程式碼先瞭解如何建立兩個類別之間的關聯性， (使用 `Post.BlogId` 屬性) 以及如何在資料庫中指定條件約束。

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

資料庫中的條件約束會顯示與之間的關聯性 `InternalBlogs.PrimaryTrackingKey` `Posts.BlogId` 。 

![InternalBlogs. PrimaryTrackingKey 與 BlogId 之間的關聯性](~/ef6/media/jj591583-figure09.png)

`InverseProperty`當您在類別之間有多個關聯性時，會使用。

在 `Post` 類別中，您可能會想要追蹤誰寫了 blog 貼文以及編輯者。 以下是 Post 類別的兩個新導覽屬性。

``` csharp
    public Person CreatedBy { get; set; }
    public Person UpdatedBy { get; set; }
```

您也需要在 `Person` 這些屬性所參考的類別中加入。 此 `Person` 類別具有導覽屬性，而該屬性是 `Post` 由人員撰寫的所有文章所撰寫，另一個則是該人員所更新的所有文章。

``` csharp
    public class Person
    {
            public int Id { get; set; }
            public string Name { get; set; }
            public List<Post> PostsWritten { get; set; }
            public List<Post> PostsUpdated { get; set; }
    }
```

Code first 無法自行比對兩個類別中的屬性。 的資料庫資料表 `Posts` 應該要有一個外鍵 `CreatedBy` ，另一個用於 `UpdatedBy` 人員，但 code first 會建立四個外鍵屬性： **person \_ Id**、 **person \_ Id1**、 **CreatedBy \_ Id** 和 **UpdatedBy \_ Id**。

![附有額外外鍵的貼文資料表](~/ef6/media/jj591583-figure10.png)

若要修正這些問題，您可以使用 `InverseProperty` 批註來指定屬性的對齊方式。

``` csharp
    [InverseProperty("CreatedBy")]
    public List<Post> PostsWritten { get; set; }

    [InverseProperty("UpdatedBy")]
    public List<Post> PostsUpdated { get; set; }
```

因為 `PostsWritten` Person 中的屬性知道這會參考型別 `Post` ，所以它會建立與的關聯性 `Post.CreatedBy` 。 同樣地， `PostsUpdated` 會連接到 `Post.UpdatedBy` 。 而且 code first 將不會建立額外的外鍵。

![Post 資料表沒有額外的外鍵](~/ef6/media/jj591583-figure11.png)

 

## <a name="summary"></a>摘要

DataAnnotations 不僅可讓您描述程式碼優先類別中的用戶端和伺服器端驗證，還可讓您根據程式碼的慣例，加強或修正 code first 針對您的類別所做的假設。 使用 DataAnnotations 時，您不僅可以驅動資料庫架構的產生，還可以將您的 code first 類別對應到既有的資料庫。

雖然它們非常有彈性，但請記住，DataAnnotations 只會提供您在 code first 類別上最常需要的設定變更。 若要針對某些邊緣案例設定您的類別，您應該尋找替代的設定機制，Code First 的流暢 API。
