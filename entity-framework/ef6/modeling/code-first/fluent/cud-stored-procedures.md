---
title: Code First 插入、更新和刪除預存程式-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 9a7ae7f9-4072-4843-877d-506dd7eef576
ms.openlocfilehash: bfc56671814aec1965ac054ff901297e5cdbbecb
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78419084"
---
# <a name="code-first-insert-update-and-delete-stored-procedures"></a>Code First 插入、更新和刪除預存程式
> [!NOTE]
> **僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。 如果您使用的是較早版本，則不適用部分或全部的資訊。  

根據預設，Code First 會將所有實體設定為使用直接資料表存取來執行 insert、update 和 delete 命令。 從 EF6 開始，您可以設定 Code First 模型，針對模型中的部分或所有實體使用預存程式。  

## <a name="basic-entity-mapping"></a>基底實體對應  

您可以使用流暢的 API，選擇使用預存程式進行插入、更新和刪除。  

``` csharp
modelBuilder
  .Entity<Blog>()
  .MapToStoredProcedures();
```  

這麼做會導致 Code First 使用某些慣例，在資料庫中建立預存程式的預期圖形。  

- 三個名為 **\<type_name\>_Insert**、 **\<type_name**\>_Update 和 **\<** type_name\>_Delete （例如 Blog_Insert、Blog_Update 和 Blog_Delete）的預存程式。  
- 參數名稱會對應到屬性名稱。  
  > [!NOTE]
  > 如果您使用 HasColumnName （）或 Column 屬性來重新命名指定屬性的資料行，則此名稱會用於參數，而不是屬性名稱。  
- **Insert 預存**程式將會有每個屬性的參數，但標示為已產生的存放區（識別或計算）除外。 預存程式應傳回結果集，其中包含每個存放區產生屬性的資料行。  
- **Update 預存**程式將會有每個屬性的參數，但以「已計算」的存放區產生模式標記的例外。 某些並行標記需要原始值的參數，請參閱下面的*並行標記*一節，以取得詳細資料。 預存程式應傳回結果集，其中包含每個計算屬性的資料行。  
- **Delete 預存**程式應具有實體索引鍵值的參數（如果實體具有複合索引鍵，則為多個參數）。 此外，delete 程式也應該具有目標資料表上任何獨立關聯外鍵的參數（實體中未宣告對應外鍵屬性的關聯性）。 某些並行標記需要原始值的參數，請參閱下面的*並行標記*一節，以取得詳細資料。  

使用下列類別做為範例：  

``` csharp
public class Blog  
{  
  public int BlogId { get; set; }  
  public string Name { get; set; }  
  public string Url { get; set; }  
}
```  

預設的預存程式如下：  

``` SQL
CREATE PROCEDURE [dbo].[Blog_Insert]  
  @Name nvarchar(max),  
  @Url nvarchar(max)  
AS  
BEGIN
  INSERT INTO [dbo].[Blogs] ([Name], [Url])
  VALUES (@Name, @Url)

  SELECT SCOPE_IDENTITY() AS BlogId
END
CREATE PROCEDURE [dbo].[Blog_Update]  
  @BlogId int,  
  @Name nvarchar(max),  
  @Url nvarchar(max)  
AS  
  UPDATE [dbo].[Blogs]
  SET [Name] = @Name, [Url] = @Url     
  WHERE BlogId = @BlogId;
CREATE PROCEDURE [dbo].[Blog_Delete]  
  @BlogId int  
AS  
  DELETE FROM [dbo].[Blogs]
  WHERE BlogId = @BlogId
```  

### <a name="overriding-the-defaults"></a>覆寫預設值  

您可以覆寫預設設定的部分或所有內容。  

您可以變更一個或多個預存程式的名稱。 這個範例只會重新命名 update 預存程式。  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.HasName("modify_blog")));
```  

這個範例會重新命名所有三個預存程式。  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.HasName("modify_blog"))  
     .Delete(d => d.HasName("delete_blog"))  
     .Insert(i => i.HasName("insert_blog")));
```  

在這些範例中，呼叫會連結在一起，但您也可以使用 lambda 區塊語法。  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    {  
      s.Update(u => u.HasName("modify_blog"));  
      s.Delete(d => d.HasName("delete_blog"));  
      s.Insert(i => i.HasName("insert_blog"));  
    });
```  

這個範例會重新命名 update 預存程式上 BlogId 屬性的參數。  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.Parameter(b => b.BlogId, "blog_id")));
```  

這些呼叫全都是可鏈結且可組合的。 以下是將所有三個預存程式及其參數重新命名的範例。  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.HasName("modify_blog")  
                   .Parameter(b => b.BlogId, "blog_id")  
                   .Parameter(b => b.Name, "blog_name")  
                   .Parameter(b => b.Url, "blog_url"))  
     .Delete(d => d.HasName("delete_blog")  
                   .Parameter(b => b.BlogId, "blog_id"))  
     .Insert(i => i.HasName("insert_blog")  
                   .Parameter(b => b.Name, "blog_name")  
                   .Parameter(b => b.Url, "blog_url")));
```  

您也可以在包含資料庫產生值的結果集中變更資料行的名稱。  

``` csharp
modelBuilder
  .Entity<Blog>()
  .MapToStoredProcedures(s =>
    s.Insert(i => i.Result(b => b.BlogId, "generated_blog_identity")));
```

``` SQL
CREATE PROCEDURE [dbo].[Blog_Insert]  
  @Name nvarchar(max),  
  @Url nvarchar(max)  
AS  
BEGIN
  INSERT INTO [dbo].[Blogs] ([Name], [Url])
  VALUES (@Name, @Url)

  SELECT SCOPE_IDENTITY() AS generated_blog_id
END
```  

## <a name="relationships-without-a-foreign-key-in-the-class-independent-associations"></a>類別中沒有外鍵的關聯性（獨立關聯）  

當外鍵屬性包含在類別定義中時，可以用與其他任何屬性相同的方式重新命名對應的參數。 當具有類別中沒有外鍵屬性的關聯性存在時，預設參數名稱會 **\<navigation_property_name\>_\<primary_key_name\>** 。  

例如，下列類別定義會導致預存程式中需要有 Blog_BlogId 參數，以插入和更新貼文。  

``` csharp
public class Blog  
{  
  public int BlogId { get; set; }  
  public string Name { get; set; }  
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

### <a name="overriding-the-defaults"></a>覆寫預設值  

您可以藉由將主鍵屬性的路徑提供給參數方法，來變更不包含在類別中的外鍵的參數。  

``` csharp
modelBuilder
  .Entity<Post>()  
  .MapToStoredProcedures(s =>  
    s.Insert(i => i.Parameter(p => p.Blog.BlogId, "blog_id")));
```  

如果您沒有相依實體的導覽屬性（亦即， [沒有 Post]。 Blog 屬性）之後，您可以使用 Association 方法來識別關聯性的另一端，然後設定對應到每個索引鍵屬性的參數。  

``` csharp
modelBuilder
  .Entity<Post>()  
  .MapToStoredProcedures(s =>  
    s.Insert(i => i.Navigation<Blog>(  
      b => b.Posts,  
      c => c.Parameter(b => b.BlogId, "blog_id"))));
```  

## <a name="concurrency-tokens"></a>並行語彙基元  

Update 和 delete 預存程式可能也需要處理平行存取：  

- 如果實體包含並行標記，則預存程式可以選擇性地具有輸出參數，以傳回已更新/已刪除的資料列數目（受影響的資料列）。 這類參數必須使用 RowsAffectedParameter 方法進行設定。  
根據預設，EF 會使用來自 ExecuteNonQuery 的傳回值來判斷受影響的資料列數目。 如果您在您的處理常式中執行任何邏輯，而導致 ExecuteNonQuery 的傳回值不正確（從 EF 的觀點來看，在執行結束時），則指定受影響的資料列輸出參數會很有用。  
- 針對每個並行標記，將會有一個名為 **\<property_name\>_Original**的參數（例如 Timestamp_Original）。 這會傳遞此屬性的原始值–從資料庫查詢時的值。  
    - 資料庫所計算的並行權杖（例如時間戳記）只會有原始值參數。  
    - 設定為並行標記的非計算屬性，在更新程式中也會有新值的參數。 這會使用已針對新值所討論的命名慣例。 例如，這類權杖的範例會使用 Blog 的 URL 做為並行存取權杖，因此需要新的值，因為您的程式碼可以將此更新為新的值（與只會由資料庫更新的時間戳記權杖不同）。  

這是範例類別，並以時間戳記並行標記更新預存程式。  

``` csharp
public class Blog  
{  
  public int BlogId { get; set; }  
  public string Name { get; set; }  
  public string Url { get; set; }  
  [Timestamp]
  public byte[] Timestamp { get; set; }
}
```

``` SQL
CREATE PROCEDURE [dbo].[Blog_Update]  
  @BlogId int,  
  @Name nvarchar(max),  
  @Url nvarchar(max),
  @Timestamp_Original rowversion  
AS  
  UPDATE [dbo].[Blogs]
  SET [Name] = @Name, [Url] = @Url     
  WHERE BlogId = @BlogId AND [Timestamp] = @Timestamp_Original
```  

以下是範例類別，並使用非計算的並行存取權杖來更新預存程式。  

``` csharp
public class Blog  
{  
  public int BlogId { get; set; }  
  public string Name { get; set; }  
  [ConcurrencyCheck]
  public string Url { get; set; }  
}
```

``` SQL
CREATE PROCEDURE [dbo].[Blog_Update]  
  @BlogId int,  
  @Name nvarchar(max),  
  @Url nvarchar(max),
  @Url_Original nvarchar(max),
AS  
  UPDATE [dbo].[Blogs]
  SET [Name] = @Name, [Url] = @Url     
  WHERE BlogId = @BlogId AND [Url] = @Url_Original
```  

### <a name="overriding-the-defaults"></a>覆寫預設值  

您可以選擇性地引進受影響資料列的參數。  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.RowsAffectedParameter("rows_affected")));
```  

針對資料庫計算的並行權杖–只會傳遞原始值–您可以只使用標準參數重新命名機制，為原始值重新具名引數。  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.Parameter(b => b.Timestamp, "blog_timestamp")));
```  

針對非計算並行標記–同時傳遞原始值和新值，您可以使用參數的多載，讓您為每個參數提供名稱。  

``` csharp
modelBuilder
 .Entity<Blog>()
 .MapToStoredProcedures(s => s.Update(u => u.Parameter(b => b.Url, "blog_url", "blog_original_url")));
```  

## <a name="many-to-many-relationships"></a>多對多關聯性  

我們將使用下列類別作為本節的範例。  

``` csharp
public class Post  
{  
  public int PostId { get; set; }  
  public string Title { get; set; }  
  public string Content { get; set; }  

  public List<Tag> Tags { get; set; }  
}  

public class Tag  
{  
  public int TagId { get; set; }  
  public string TagName { get; set; }  

  public List<Post> Posts { get; set; }  
}
```  

多對多關聯性可以使用下列語法對應至預存程式。  

``` csharp
modelBuilder  
  .Entity<Post>()  
  .HasMany(p => p.Tags)  
  .WithMany(t => t.Posts)  
  .MapToStoredProcedures();
```  

如果未提供其他設定，則預設會使用下列「預存程式」圖形。  

- 名為\<的兩個預存程式**type_one\>\<type_two**\>_Insert 和 **\<type_one**\>\<type_two\>_Delete （例如 PostTag_Insert 和 PostTag_Delete）。  
- 這些參數將會是每個類型的索引鍵值。 \<的每個參數名稱**type_name\>_\<property_name\>** （例如 Post_PostId 和 Tag_TagId）。

以下是 insert 和 update 預存程式的範例。  

``` SQL
CREATE PROCEDURE [dbo].[PostTag_Insert]  
  @Post_PostId int,  
  @Tag_TagId int  
AS  
  INSERT INTO [dbo].[Post_Tags] (Post_PostId, Tag_TagId)   
  VALUES (@Post_PostId, @Tag_TagId)
CREATE PROCEDURE [dbo].[PostTag_Delete]  
  @Post_PostId int,  
  @Tag_TagId int  
AS  
  DELETE FROM [dbo].[Post_Tags]    
  WHERE Post_PostId = @Post_PostId AND Tag_TagId = @Tag_TagId
```  

### <a name="overriding-the-defaults"></a>覆寫預設值  

您可以使用與實體預存程式類似的方式來設定程式和參數名稱。  

``` csharp
modelBuilder  
  .Entity<Post>()  
  .HasMany(p => p.Tags)  
  .WithMany(t => t.Posts)  
  .MapToStoredProcedures(s =>  
    s.Insert(i => i.HasName("add_post_tag")  
                   .LeftKeyParameter(p => p.PostId, "post_id")  
                   .RightKeyParameter(t => t.TagId, "tag_id"))  
     .Delete(d => d.HasName("remove_post_tag")  
                   .LeftKeyParameter(p => p.PostId, "post_id")  
                   .RightKeyParameter(t => t.TagId, "tag_id")));
```  
