---
title: 建構函式 EF Core與實體類型
author: ajcvickers
ms.author: divega
ms.date: 02/23/2018
ms.assetid: 420AFFE7-B709-4A68-9149-F06F8746FB33
ms.technology: entity-framework-core
uid: core/modeling/constructors
ms.openlocfilehash: 8cea624c295f99ef54cb8b4758642eade03c235e
ms.sourcegitcommit: 507a40ed050fee957bcf8cf05f6e0ec8a3b1a363
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/26/2018
ms.locfileid: "31812491"
---
# <a name="entity-types-with-constructors"></a>使用建構函式的實體類型

> [!NOTE]  
> 這項功能是在 EF Core 2.1 中新功能。

從開始 EF Core 2.1，您就可以定義參數的建構函式，並已建立實體的執行個體時呼叫這個建構函式的 EF Core。 建構函式參數可以繫結至對應的屬性，或為各種類型的服務，以促進行為類似消極式載入。

> [!NOTE]  
> EF Core 2.1 中，為所有的建構函式繫結的慣例。 若要使用的特定建構函式的組態已規劃在未來的版本。

## <a name="binding-to-mapped-properties"></a>繫結至對應的屬性

請考量一般的部落格文章/模型：

```Csharp
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

當 EF Core建立這些類型的執行個體時，例如結果的查詢時，它會先呼叫預設的無參數建構函式，然後設定每一個屬性的值從資料庫。 不過，如果 EF Core找到的參數化建構函式參數名稱和相符的型別對應屬性，則它會改為呼叫這些屬性值的參數化建構函式，並將未明確設定每個屬性。 例如: 

```Csharp
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
請注意一些事項：
* 並非所有屬性都必須具有建構函式參數。 比方說，Post.Content 是未設定屬性的任何建構函式參數，因此 EF Core之後呼叫建構函式以一般方式將設定。
* 參數型別和名稱必須符合屬性類型和名稱，不同之處在於屬性可以是依照 pascal 命名法的大小寫慣例而參數是依照 camel 命名法的大小寫慣例。
* EF Core無法設定 （例如部落格或上述文章） 的導覽屬性使用建構函式。
* 建構函式可以是公用、 私用，或有任何其他協助工具。

### <a name="read-only-properties"></a>唯讀屬性

一旦透過建構函式設定屬性之後將可以有意義將其中部分為唯讀。 EF Core支援，但要留意的一些事項：
* 沒有 setter 的屬性未對應的慣例。 （這樣通常會以對應不應對應，例如計算屬性的屬性。）
* 使用自動產生索引鍵的值必須是讀寫，因為插入新的實體時，金鑰產生器所要設定需要的索引鍵值的索引鍵屬性。

避免這些作業的簡單方法是使用私用 setter。 例如: 
```Csharp
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
EF Core看見為讀寫，也就是說，所有的屬性對應與之前，以及索引鍵可能仍會存放區產生的私用 setter 的屬性。

使用私用 setter 的替代方式是將屬性實際上是唯讀，並新增 OnModelCreating 更明確的對應。 同樣地，某些屬性可以完全移除並取代欄位。 例如，請考慮這些實體類型：

```Csharp
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
和 OnModelCreating 中的此設定：
```Csharp
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
必須注意的事項：
* 金鑰 「 屬性 」 現在是欄位。 它不是`readonly`欄位，以便可以使用存放區產生的索引鍵。 
* 其他屬性是唯讀屬性只在建構函式中設定。
* 如果只有 EF 來設定或讀取資料庫的主索引鍵值，就不需要包含於建構函式。 這讓 「 屬性 」 的索引鍵保持為簡單欄位，並使其清除，它應該不明確設定時建立新的部落格或文章。

> [!NOTE]  
> 此程式碼會產生編譯器警告 '169' 表示永遠不會使用此欄位。 因為事實上 EF Core使用的欄位 extralinguistic 的方式可以忽略。

## <a name="injecting-services"></a>插入服務

EF Core也可以將 「 服務 」 插入到之實體類型的建構函式。 例如，下列可插入：
* `DbContext` -目前的內容執行個體，這也可輸入為衍生的 DbContext 類型
* `ILazyLoader` -消極式載入服務-請參閱[消極式載入文件](../querying/related-data.md)如需詳細資訊
* `Action<object, string>` -消極式載入委派-請參閱[消極式載入文件](../querying/related-data.md)如需詳細資訊
* `IEntityType` -此實體類型相關聯的 EF Core中繼資料

> [!NOTE]  
> 為準，EF Core 2.1 中，可插入 EF Core 的已知的服務。 用於將應用程式服務的支援是正在考慮未來的版本。

例如，插入的 DbContext 可用來選擇性地存取資料庫，以取得相關實體的相關資訊，而不會載入所有它們。 在下列範例中，這用來取得而不必載入張貼的部落格文章的數目：

```Csharp
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
請注意相關的幾件事：
* 因為它永遠只會呼叫 EF Core，而且沒有其他公用建構函式一般用途，是私人的建構函式。
* 使用插入的服務的程式碼 （也就是內容） 會針對它防禦正在`null`來處理一些情況其中 EF Core不會建立執行個體。
* 因為服務儲存在讀/寫屬性，如果實體附加到新的內容執行個體，它會被重設。

> [!WARNING]  
> 插入如下 DbContext 是通常會視為反面模式，因為直接到 EF Core 涉入實體類型。 使用像這樣的服務資料隱碼之前，請審慎考慮所有選項。
