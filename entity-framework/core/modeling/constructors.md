---
title: 具有函式的實體類型-EF Core
description: 使用函式搭配 Entity Framework Core 模型來系結資料
author: ajcvickers
ms.date: 02/23/2018
ms.assetid: 420AFFE7-B709-4A68-9149-F06F8746FB33
uid: core/modeling/constructors
ms.openlocfilehash: 58529a3a68e69a31249460d402027274404dce45
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617534"
---
# <a name="entity-types-with-constructors"></a>具有函式的實體類型

> [!NOTE]  
> 此功能是 EF Core 2.1 中的新功能。

從 EF Core 2.1 開始，現在可以定義具有參數的函式，並在建立實體的實例時，EF Core 呼叫這個函式。 這些函式參數可以系結至對應的屬性，或系結至各種不同的服務，以促進延遲載入等行為。

> [!NOTE]  
> 從 EF Core 2.1，所有的函式系結都是依照慣例。 未來的版本計畫會設定要使用的特定函式。

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

當 EF Core 建立這些型別的實例時（例如查詢結果），它會先呼叫預設的無參數的函式，然後將每個屬性設定為資料庫中的值。 但是，如果 EF Core 找到參數名稱和類型符合對應屬性的參數化的函式，則會改為使用這些屬性的值來呼叫參數化的函式，而不會明確地設定每個屬性。 例如：

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

注意事項如下：

* 並非所有屬性都必須有函式參數。 例如，任何的函式參數都不會設定 Content-type 屬性，因此 EF Core 會在以正常方式呼叫該函式之後進行設定。
* 參數型別和名稱必須符合屬性型別和名稱，不同之處在于屬性可以是 Pascal 大小寫，而參數的大小寫則是 camel。
* EF Core 無法使用「函式」來設定導覽屬性 (例如) 的 Blog 或貼文。
* 此函數可以是公用、私用或具有任何其他存取範圍。 不過，消極式載入 proxy 需要可從繼承 proxy 類別存取的函式。 這通常表示它是公開或受保護的。

### <a name="read-only-properties"></a>唯讀屬性

一旦屬性是透過函式進行設定，就有可能讓某些屬性成為唯讀。 EF Core 支援這項功能，但有一些需要注意的事項：

* 沒有 setter 的屬性不會依照慣例進行對應。  (這麼做，通常會對應不應該對應的屬性，例如計算的屬性。 ) 
* 使用自動產生的索引鍵值需要可讀寫的索引鍵屬性，因為在插入新的實體時，金鑰產生器需要設定金鑰值。

避免這些事的簡單方法是使用私用 setter。 例如：

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

EF Core 看到具有私用 setter 的屬性為讀寫，這表示所有屬性都與之前對應，而且索引鍵仍可以存放區產生。

使用私用 setter 的另一種方式，是讓屬性真正是唯讀的，並在 OnModelCreating 中新增更明確的對應。 同樣地，某些屬性可以完全移除，並且只以欄位取代。 例如，請考慮下列實體類型：

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

此外，OnModelCreating 中的這項設定：

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

* 索引鍵 "property" 現在是一個欄位。 它不是 `readonly` 欄位，因此可以使用存放區產生的索引鍵。
* 其他屬性則是唯讀屬性，只會在此函式中設定。
* 如果主要金鑰值只是由 EF 設定或從資料庫讀取，則不需要將它包含在函式中。 這會將機碼 "property" 保留為簡單的欄位，並清楚指出在建立新的 blog 或 post 時，不應該明確地設定它。

> [!NOTE]  
> 這段程式碼會產生編譯器警告 ' 169 '，指出從未使用過此欄位。 這可以被忽略，因為在現實中 EF Core 會以 extralinguistic 的方式使用欄位。

## <a name="injecting-services"></a>插入服務

EF Core 也可以在實體類型的函式中插入 "services"。 例如，您可以插入下列內容：

* `DbContext` -目前的內容實例，也可以輸入為您的衍生 DbCoNtext 型別
* `ILazyLoader`-延遲載入服務--如需詳細資料，請參閱消極[式載入檔](xref:core/querying/related-data)
* `Action<object, string>`-消極式載入委派--如需詳細資料，請參閱消極[式載入檔](xref:core/querying/related-data)
* `IEntityType` -與此實體類型相關聯的 EF Core 中繼資料

> [!NOTE]  
> 從 EF Core 2.1 中，只能插入 EF Core 已知的服務。 未來版本會考慮插入應用程式服務的支援。

例如，插入的 DbCoNtext 可以用來選擇性地存取資料庫，以取得相關實體的相關資訊，而不將它們全部載入。 在下列範例中，這是用來取得 blog 中的貼文數目，而不需要載入文章：

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

* 此函式是私用的，因為它只會由 EF Core 呼叫，而且有另一個公用的函式可供一般用途使用。
* 使用插入服務的程式碼 (也就是，內容) 是防禦的，因為它會 `null` 處理 EF Core 不是建立實例的情況。
* 由於服務是儲存在讀取/寫入屬性中，因此當實體附加至新的內容實例時，將會重設。

> [!WARNING]  
> 像這樣插入 DbCoNtext 通常會被視為反模式，因為它會將您的實體類型直接結合 EF Core。 使用像這樣的服務插入之前，請仔細考慮所有選項。
