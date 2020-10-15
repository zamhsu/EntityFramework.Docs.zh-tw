---
title: Code First Insert、Update 和 Delete 預存程式-EF6
description: Code First Entity Framework 6 中的插入、更新和刪除預存程式
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/code-first/fluent/cud-stored-procedures
ms.openlocfilehash: 749bcc9faaf92ae6555c37facb8de31d877d1260
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065182"
---
# <a name="code-first-insert-update-and-delete-stored-procedures"></a>Code First 插入、更新和刪除預存程式
> [!NOTE]
> **僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。 如果您使用的是較早版本，則不適用部分或全部的資訊。  

根據預設，Code First 會將所有實體設定為使用直接資料表存取來執行插入、更新和刪除命令。 從 EF6 開始，您可以設定 Code First 模型，以針對模型中的部分或所有實體使用預存程式。  

## <a name="basic-entity-mapping"></a>基底實體對應  

您可以使用流暢的 API，選擇使用預存程式進行插入、更新和刪除。  

``` csharp
modelBuilder
  .Entity<Blog>()
  .MapToStoredProcedures();
```  

這麼做會導致 Code First 使用某些慣例，在資料庫中建立預存程式的預期圖形。  

- 有三個名為** \<type_name\> _Insert**、 ** \<type_name\> _Update**和** \<type_name\> _Delete** (的預存程式，例如 Blog_Insert、Blog_Update 和 Blog_Delete) 。  
- 參數名稱會對應至屬性名稱。  
  > [!NOTE]
  > 如果您使用 HasColumnName ( # A1 或資料行屬性來重新命名指定屬性的資料行，則此名稱會用於參數，而不是屬性名稱。  
- **插入預存** 程式將會有每個屬性的參數，但標示為存放區的 (識別或計算) 。 預存程式應該會傳回結果集，其中包含每個存放區產生屬性的資料行。  
- **更新預存** 程式將會有每個屬性的參數，但標有存放區產生之「計算」模式的標記除外。 某些並行標記需要原始值的參數，請參閱下方的 *並行標記* 一節以取得詳細資料。 預存程式應該會傳回結果集，其中包含每個計算屬性的資料行。  
- 如果實體有複合索引鍵) ，**刪除預存**程式應該要有實體 (或多個參數的索引鍵值參數。 此外，在目標資料表上，delete 程式也應該有任何獨立關聯外鍵的參數 (不具有在實體) 中宣告之對應外鍵屬性的關聯性。 某些並行標記需要原始值的參數，請參閱下方的 *並行標記* 一節以取得詳細資料。  

使用下列類別做為範例：  

``` csharp
public class Blog  
{  
  public int BlogId { get; set; }  
  public string Name { get; set; }  
  public string Url { get; set; }  
}
```  

預設的預存程式為：  

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

您可以變更一個或多個預存程式的名稱。 此範例只會重新具名更新預存程式。  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.HasName("modify_blog")));
```  

此範例會重新命名所有三個預存程式。  

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

這個範例會重新具名更新預存程式上 BlogId 屬性的參數。  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.Parameter(b => b.BlogId, "blog_id")));
```  

這些呼叫都是可鏈結且可組合的。 以下是重新命名所有三個預存程式及其參數的範例。  

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

您也可以變更結果集中包含資料庫產生之值的資料行名稱。  

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

## <a name="relationships-without-a-foreign-key-in-the-class-independent-associations"></a>類別中沒有外鍵的關聯性 (獨立關聯)   

當類別定義中包含外鍵屬性時，可以使用與任何其他屬性相同的方式重新命名對應的參數。 當關聯性不存在類別中的外鍵屬性時，預設參數名稱為** \<navigation_property_name\> _ \<primary_key_name\> **。  

例如，下列類別定義會導致在預存程式中需要 Blog_BlogId 參數，以插入和更新貼文。  

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

您可以藉由提供主要索引鍵屬性的路徑給參數方法，來變更不包含在類別中的外鍵的參數。  

``` csharp
modelBuilder
  .Entity<Post>()  
  .MapToStoredProcedures(s =>  
    s.Insert(i => i.Parameter(p => p.Blog.BlogId, "blog_id")));
```  

如果您沒有相依實體的導覽屬性 (即 沒有 Post。 Blog 屬性) 接著您可以使用 Association 方法來識別關聯性的另一端，然後設定對應至每個索引鍵屬性 (s) 的參數。  

``` csharp
modelBuilder
  .Entity<Post>()  
  .MapToStoredProcedures(s =>  
    s.Insert(i => i.Navigation<Blog>(  
      b => b.Posts,  
      c => c.Parameter(b => b.BlogId, "blog_id"))));
```  

## <a name="concurrency-tokens"></a>並行語彙基元  

更新和刪除預存程式可能也需要處理平行存取：  

- 如果實體包含並行 token，則預存程式可以選擇性地有 output 參數，以傳回受影響) 的 (資料列更新/刪除的資料列數目。 這類參數必須使用 RowsAffectedParameter 方法來設定。  
根據預設，EF 會使用來自 ExecuteNonQuery 的傳回值來判斷受影響的資料列數目。 如果您在您的執行程式中執行任何邏輯，會導致 ExecuteNonQuery 的傳回值不正確 (從 EF 在執行結束時) 的觀點，則指定受影響的資料列輸出參數會很有用。  
- 針對每個並行權杖，會有一個名為** \<property_name\> _Original**的參數 (例如 Timestamp_Original ) 。 這會傳遞這個屬性的原始值–從資料庫查詢時的值。  
    - 資料庫所計算的並行權杖（例如時間戳記）只會有原始值參數。  
    - 設定為並行標記的非計算屬性也會有 update 程式中新值的參數。 這會使用已針對新值討論過的命名慣例。 這類權杖的其中一個範例是使用 Blog 的 URL 做為並行存取權杖，因為您的程式碼可以將其更新為新的值，所以需要新的值， (與只由資料庫) 更新的時間戳記權杖不同。  

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

以下是範例類別，並使用非計算的並行標記更新預存程式。  

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

您可以選擇性地引進受影響的資料列參數。  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.RowsAffectedParameter("rows_affected")));
```  

針對資料庫計算的並行存取權杖（只有傳遞原始值的位置），您可以只使用標準參數重新命名機制來重新命名原始值的參數。  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.Parameter(b => b.Timestamp, "blog_timestamp")));
```  

針對非計算的並行存取權杖（同時傳遞原始值和新值），您可以使用參數的多載，讓您提供每個參數的名稱。  

``` csharp
modelBuilder
 .Entity<Blog>()
 .MapToStoredProcedures(s => s.Update(u => u.Parameter(b => b.Url, "blog_url", "blog_original_url")));
```  

## <a name="many-to-many-relationships"></a>多對多關聯性  

在本節中，我們將使用下列類別做為範例。  

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

您可以使用下列語法，將多對多關聯性對應至預存程式。  

``` csharp
modelBuilder  
  .Entity<Post>()  
  .HasMany(p => p.Tags)  
  .WithMany(t => t.Posts)  
  .MapToStoredProcedures();
```  

如果未提供其他設定，則預設會使用下列預存程式圖形。  

- 有兩個名為** \<type_one\> \<type_two\> _Insert**和** \<type_one\> \<type_two\> _Delete** (的預存程式，例如 PostTag_Insert 和 PostTag_Delete) 。  
- 參數將會是每個類型)  (s 的索引鍵值。 每個參數的名稱為** \<type_name\> _ \<property_name\> ** (例如，Post_PostId 和 Tag_TagId) 。

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

您可以使用類似的方式，將程式和參數名稱設定為實體預存程式。  

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
