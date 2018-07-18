---
title: 程式碼的第一個 Insert、 Update 和 Delete 預存程序-EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 9a7ae7f9-4072-4843-877d-506dd7eef576
caps.latest.revision: 3
ms.openlocfilehash: 1f100ed888abd98df83c80d0de2086cfb1ba7b4f
ms.sourcegitcommit: bdd06c9a591ba5e6d6a3ec046c80de98f598f3f3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2018
ms.locfileid: "39120551"
---
# <a name="code-first-insert-update-and-delete-stored-procedures"></a>程式碼的第一個 Insert、 Update 和 Delete 預存程序
> [!NOTE]
> **僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。 如果您使用的是較早版本，則不適用部分或全部的資訊。  

根據預設，Code First 會設定所有的實體執行插入、 更新和刪除使用直接存取資料表的命令。 從開始，在 EF6 中您可以設定您要用於模型中的部分或所有實體的預存程序的 Code First 模型。  

## <a name="basic-entity-mapping"></a>基本的實體對應  

您可以選擇使用預存程序插入、 更新和刪除使用 Fluent API。  

``` csharp
modelBuilder
  .Entity<Blog>()
  .MapToStoredProcedures();
```  

這麼做會導致 Code First 使用部分慣例來建立預存程序，在資料庫中預期的圖形。  

- 三個預存程序名為 **\<type_name\>（_i)**，  **\<type_name\>（_u)** 並 **\<type_名稱\>（_d)** （比方說，Blog_Insert、 Blog_Update 和 Blog_Delete）。  
- 參數名稱會對應到屬性名稱。  
  > [!NOTE]
  > 如果您使用 HasColumnName() 或資料行屬性來指定屬性資料行重新命名此名稱將用於參數，而不是屬性名稱。  
- **Insert 預存程序**會有每個屬性，除了標示為儲存所產生的參數 (身分識別或計算)。 預存程序應該會傳回具有儲存所產生的每個屬性的資料行的結果集。  
- **更新預存程序**會有每個屬性，除了以儲存所產生的模式 'Computed' 標記的參數。 某些並行語彙基元需要原始值的參數，請參閱 <<c0>  *並行語彙基元*如需詳細資訊，如下一節。 預存程序應該傳回的結果集資料行的每個計算的屬性。  
- **刪除預存程序**應有的索引鍵值的實體 （或多個參數，如果實體有複合索引鍵） 的參數。 此外，刪除程序也應具有參數的任何獨立關聯中的外部索引鍵會在目標資料表 （不需要對應外部索引鍵屬性宣告的實體中的關聯性）。 某些並行語彙基元需要原始值的參數，請參閱 <<c0>  *並行語彙基元*如需詳細資訊，如下一節。  

您可以使用下列類別做為範例：  

``` csharp
public class Blog  
{  
  public int BlogId { get; set; }  
  public string Name { get; set; }  
  public string Url { get; set; }  
}
```  

預存程序就是預設值：  

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

您可以覆寫部分或所有預設已設定的項目。  

您可以變更一或多個預存程序的名稱。 此範例中重新命名只更新預存程序。  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.HasName("modify_blog")));
```  

此範例中重新命名所有的三個預存程序。  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.HasName("modify_blog"))  
     .Delete(d => d.HasName("delete_blog"))  
     .Insert(i => i.HasName("insert_blog")));
```  

在這些範例中，會呼叫鏈結在一起，但您也可以使用 lambda 區塊的語法。  

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

此範例中重新命名 BlogId 屬性上更新預存程序的參數。  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.Parameter(b => b.BlogId, "blog_id")));
```  

這些呼叫都可鏈結且可組合。 以下是重新命名所有的三個預存程序和其參數的範例。  

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

您也可以變更中包含的資料庫產生值的結果集的資料行的名稱。  

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

## <a name="relationships-without-a-foreign-key-in-the-class-independent-associations"></a>沒有類別 （獨立關聯） 中的外部索引鍵的關聯性  

在類別定義中包含外部索引鍵屬性，則對應的參數可以重新命名為任何其他屬性相同的方式。 在類別中的外部索引鍵屬性不存在的關聯性，預設的參數名稱時，  **\<navigation_property_name\>_\<primary_key_name\>**。  

例如，下列類別定義會導致預存程序，來插入及更新的文章中所預期的 Blog_BlogId 參數。  

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

您可以變更參數所提供的參數方法的主索引鍵屬性的路徑不包含在類別中的外部索引鍵。  

``` csharp
modelBuilder
  .Entity<Post>()  
  .MapToStoredProcedures(s =>  
    s.Insert(i => i.Parameter(p => p.Blog.BlogId, "blog_id")));
```  

如果您沒有相依的實體 （也就是導覽屬性 沒有 Post.Blog 屬性） 則您可以使用關聯的方法來識別關聯性另一端，然後設定 參數對應至每個索引鍵的屬性。  

``` csharp
modelBuilder
  .Entity<Post>()  
  .MapToStoredProcedures(s =>  
    s.Insert(i => i.Navigation<Blog>(  
      b => b.Posts,  
      c => c.Parameter(b => b.BlogId, "blog_id"))));
```  

## <a name="concurrency-tokens"></a>並行語彙基元  

Update 和 delete 預存程序可能也需要應付並行處理：  

- 如果實體包含並行語彙基元，預存程序可以選擇性地具有輸出參數傳回的資料列更新/刪除 （受影響的資料列） 數目。 這類參數必須設為使用 RowsAffectedParameter 方法。  
根據預設 EF 會使用 ExecuteNonQuery 的傳回值，來判斷多少資料列受到影響。 指定受影響的資料列的輸出參數會很有用，如果您執行的任何邏輯 ExecuteNonQuery 正在 （從 EF 的觀點來看） 不正確的傳回值會導致您預存程序中執行的結尾。  
- 針對每個並行有語彙基元會名為的參數 **\<property_name\>_Original** (比方說，Timestamp_Original)。 這會傳遞值時從資料庫查詢 – 此屬性的原始值。  
    - 並行語彙基元計算的資料集，例如時間戳記 – 只有原始的實值參數。  
    - 非計算的屬性設定為並行語彙基元也會更新程序中有新的值的參數。 這會使用已經討論過的新值的命名慣例。 這類權杖的範例會為並行語彙基元使用部落格的 URL，因為這可由您的程式碼 （不同於這只會由資料庫更新的時間戳記語彙基元） 更新為新值，則需要新的值。  

這是範例類別，並更新預存程序具有時間戳記的並行語彙基元。  

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

以下是範例類別，並更新預存程序與非計算的並行語彙基元。  

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

您可以選擇性地導入受影響的資料列參數。  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.RowsAffectedParameter("rows_affected")));
```  

針對計算的資料庫並行語彙基元 – 其中只有原始的值會傳遞 – 您可以只使用標準的參數重新命名的機制來重新命名原始值的參數。  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.Parameter(b => b.Timestamp, "blog_timestamp")));
```  

針對非計算的並行語彙基元 – 其中同時的原始和新值會傳遞 – 您可以使用多載的參數，可讓您提供每個參數的名稱。  

``` csharp
modelBuilder
 .Entity<Blog>()
 .MapToStoredProcedures(s => s.Update(u => u.Parameter(b => b.Url, "blog_url", "blog_original_url")));
```  

## <a name="many-to-many-relationships"></a>多對多關聯性  

我們將使用下列類別做為範例，這一節。  

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

可以使用下列語法的預存程序對應多對多關聯性。  

``` csharp
modelBuilder  
  .Entity<Post>()  
  .HasMany(p => p.Tags)  
  .WithMany(t => t.Posts)  
  .MapToStoredProcedures();
```  

如果沒有提供任何其他設定則會使用預設的下列預存程序的圖形。  

- 兩個預存程序名為 **\<type_one\>\<type_two\>（_i)** 並 **\<type_one\>\<type_two\>（_d)** （比方說，PostTag_Insert 和 PostTag_Delete）。  
- 參數會針對每個類型的索引鍵的值。 之每個參數的名稱**\<type_name\>_\<property_name\>** （比方說，Post_PostId 和 Tag_TagId）。

以下是範例插入和更新預存程序。  

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

類似的方式，來儲存實體的程序，可以設定的程序和參數名稱。  

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
