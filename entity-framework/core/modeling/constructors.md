---
title: 具有函數的實體類型-EF Core
author: ajcvickers
ms.date: 02/23/2018
ms.assetid: 420AFFE7-B709-4A68-9149-F06F8746FB33
uid: core/modeling/constructors
ms.openlocfilehash: ddfaa8eebde388a9d3309f21b8891de593077956
ms.sourcegitcommit: 2355447d89496a8ca6bcbfc0a68a14a0bf7f0327
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2019
ms.locfileid: "72811890"
---
# <a name="entity-types-with-constructors"></a>具有函數的實體類型

> [!NOTE]  
> 此功能是 EF Core 2.1 中的新功能。

從 EF Core 2.1 開始，現在可以定義具有參數的處理常式，並在建立實體的實例時，讓 EF Core 呼叫此函式。 此函式參數可以系結至對應的屬性，或系結至各種不同的服務，以促進延遲載入之類的行為。

> [!NOTE]  
> 從 EF Core 2.1，所有的函式系結都是依照慣例。 未來的版本已規劃要使用的特定構造函式設定。

## <a name="binding-to-mapped-properties"></a>系結至對應的屬性

請考慮一般的 Blog/Post 模型：

``` csharp
public class Blog
{
    public int Id { get; set; }

    public string Name { get; set; }
    public string Author { get; set; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    public int Id { get; set; }

    public string Title { get; set; }
    public string Content { get; set; }
    public DateTime PostedOn { get; set; }

    public Blog Blog { get; set; }
}
```

當 EF Core 建立這些類型的實例時（例如，針對查詢的結果），它會先呼叫預設的無參數的函式，然後將每個屬性設定為資料庫中的值。 不過，如果 EF Core 使用符合對應屬性之參數名稱和類型來尋找參數化的函式，則會改為使用這些屬性的值來呼叫參數化的函式，而且不會明確設定每個屬性。 例如:

``` csharp
public class Blog
{
    public Blog(int id, string name, string author)
    {
        Id = id;
        Name = name;
        Author = author;
    }

    public int Id { get; set; }

    public string Name { get; set; }
    public string Author { get; set; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    public Post(int id, string title, DateTime postedOn)
    {
        Id = id;
        Title = title;
        PostedOn = postedOn;
    }

    public int Id { get; set; }

    public string Title { get; set; }
    public string Content { get; set; }
    public DateTime PostedOn { get; set; }

    public Blog Blog { get; set; }
}
```

要注意的事項：

* 並非所有屬性都必須具有函數參數。 例如，不是由任何的「函數參數」設定「內容」屬性，因此 EF Core 會在以一般方式呼叫「函式」之後設定它。
* 參數類型和名稱必須符合屬性類型和名稱，但屬性可以是 Pascal 大小寫，而參數則是 camel 大小寫。
* EF Core 無法使用函式來設定導覽屬性（例如上述的 Blog 或 Post）。
* 此函式可以是公用、私用或具有任何其他存取範圍。 不過，消極式載入 proxy 需要可從繼承 proxy 類別存取的函式。 這通常表示將它設為公用或受保護。

### <a name="read-only-properties"></a>唯讀屬性

透過函式設定屬性之後，將其部分設為唯讀可能是合理的。 EF Core 支援這種情況，但有一些要注意的事項：

* 沒有 setter 的屬性不會依照慣例來對應。 （這麼做通常會對應不應對應的屬性，例如計算屬性）。
* 使用自動產生的索引鍵值需要具有讀寫的索引鍵屬性，因為在插入新的實體時，索引鍵的值必須由金鑰產生器設定。

避免這些問題的簡單方法是使用私用 setter。 例如:

``` csharp
public class Blog
{
    public Blog(int id, string name, string author)
    {
        Id = id;
        Name = name;
        Author = author;
    }

    public int Id { get; private set; }

    public string Name { get; private set; }
    public string Author { get; private set; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    public Post(int id, string title, DateTime postedOn)
    {
        Id = id;
        Title = title;
        PostedOn = postedOn;
    }

    public int Id { get; private set; }

    public string Title { get; private set; }
    public string Content { get; set; }
    public DateTime PostedOn { get; private set; }

    public Blog Blog { get; set; }
}
```

EF Core 看到具有私用 setter 的屬性為讀寫，這表示所有屬性都對應于前面，而且金鑰仍然可以存放區產生。

使用私用 setter 的替代方法是讓屬性成為唯讀的，並在 OnModelCreating 中新增更明確的對應。 同樣地，某些屬性可以完全移除，而且只會以欄位取代。 例如，請考慮下列實體類型：

``` csharp
public class Blog
{
    private int _id;

    public Blog(string name, string author)
    {
        Name = name;
        Author = author;
    }

    public string Name { get; }
    public string Author { get; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    private int _id;

    public Post(string title, DateTime postedOn)
    {
        Title = title;
        PostedOn = postedOn;
    }

    public string Title { get; }
    public string Content { get; set; }
    public DateTime PostedOn { get; }

    public Blog Blog { get; set; }
}
```

在 OnModelCreating 中的這項設定：

``` csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>(
        b =>
        {
            b.HasKey("_id");
            b.Property(e => e.Author);
            b.Property(e => e.Name);
        });

    modelBuilder.Entity<Post>(
        b =>
        {
            b.HasKey("_id");
            b.Property(e => e.Title);
            b.Property(e => e.PostedOn);
        });
}
```

注意事項：

* 索引鍵 "property" 現在是欄位。 這不是 `readonly` 欄位，因此可以使用存放區產生的索引鍵。
* 其他屬性是唯讀屬性，只會在此函式中設定。
* 如果主要金鑰值只是由 EF 設定或從資料庫讀取，則不需要將它包含在此函式中。 這會將金鑰「屬性」保留為簡單的欄位，並清楚指出不應該在建立新的 blog 或文章時明確設定。

> [!NOTE]  
> 這段程式碼會產生編譯器警告 ' 169 '，表示從未使用過此欄位。 這可能會被忽略，因為事實上，EF Core 以 extralinguistic 的方式使用欄位。

## <a name="injecting-services"></a>插入服務

EF Core 也可以在實體類型的函式中插入「服務」。 例如，您可以插入下列內容：

* `DbContext`-目前的內容實例，也可以輸入為衍生的 DbCoNtext 類型
* `ILazyLoader`-延遲載入服務--如需詳細資訊，請參閱消極[式載入檔](../querying/related-data.md)
* `Action<object, string>`-延遲載入委派--如需詳細資料，請參閱消極[式載入檔](../querying/related-data.md)
* `IEntityType`-與此實體類型相關聯的 EF Core 中繼資料

> [!NOTE]  
> 從 EF Core 2.1，只有 EF Core 已知的服務可以插入。 未來版本將會考慮插入應用程式服務的支援。

例如，插入的 DbCoNtext 可以用來選擇性地存取資料庫，以取得相關實體的資訊，而不需全部載入。 在下列範例中，這是用來取得 blog 中的貼文數目，而不需要載入文章：

``` csharp
public class Blog
{
    public Blog()
    {
    }

    private Blog(BloggingContext context)
    {
        Context = context;
    }

    private BloggingContext Context { get; set; }

    public int Id { get; set; }
    public string Name { get; set; }
    public string Author { get; set; }

    public ICollection<Post> Posts { get; set; }

    public int PostsCount
        => Posts?.Count
           ?? Context?.Set<Post>().Count(p => Id == EF.Property<int?>(p, "BlogId"))
           ?? 0;
}

public class Post
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }
    public DateTime PostedOn { get; set; }

    public Blog Blog { get; set; }
}
```

以下是一些要注意的事項：

* 此函式是私用的，因為它只會由 EF Core 呼叫，而且有另一個一般使用的公用函數。
* 使用插入的服務（也就是內容）的程式碼，可以防禦它 `null`，以處理 EF Core 不會建立實例的情況。
* 因為服務是儲存在讀取/寫入屬性中，所以當實體附加至新的內容實例時，將會重設它。

> [!WARNING]  
> 插入這類 DbCoNtext 通常會被視為反模式，因為它會直接將您的實體類型結合 EF Core。 在使用這類服務插入之前，請先仔細考慮所有選項。
