---
title: 程式碼的第一個 Insert、 Update 和 Delete 預存程序-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 9a7ae7f9-4072-4843-877d-506dd7eef576
ms.openlocfilehash: bfc56671814aec1965ac054ff901297e5cdbbecb
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2018
ms.locfileid: "45489618"
---
# <a name="code-first-insert-update-and-delete-stored-procedures"></a><span data-ttu-id="2da50-102">程式碼的第一個 Insert、 Update 和 Delete 預存程序</span><span class="sxs-lookup"><span data-stu-id="2da50-102">Code First Insert, Update, and Delete Stored Procedures</span></span>
> [!NOTE]
> <span data-ttu-id="2da50-103">**僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。</span><span class="sxs-lookup"><span data-stu-id="2da50-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="2da50-104">如果您使用的是較早版本，則不適用部分或全部的資訊。</span><span class="sxs-lookup"><span data-stu-id="2da50-104">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="2da50-105">根據預設，Code First 會設定所有的實體執行插入、 更新和刪除使用直接存取資料表的命令。</span><span class="sxs-lookup"><span data-stu-id="2da50-105">By default, Code First will configure all entities to perform insert, update and delete commands using direct table access.</span></span> <span data-ttu-id="2da50-106">從開始，在 EF6 中您可以設定您要用於模型中的部分或所有實體的預存程序的 Code First 模型。</span><span class="sxs-lookup"><span data-stu-id="2da50-106">Starting in EF6 you can configure your Code First model to use stored procedures for some or all entities in your model.</span></span>  

## <a name="basic-entity-mapping"></a><span data-ttu-id="2da50-107">基本的實體對應</span><span class="sxs-lookup"><span data-stu-id="2da50-107">Basic Entity Mapping</span></span>  

<span data-ttu-id="2da50-108">您可以選擇使用預存程序插入、 更新和刪除使用 Fluent API。</span><span class="sxs-lookup"><span data-stu-id="2da50-108">You can opt into using stored procedures for insert, update and delete using the Fluent API.</span></span>  

``` csharp
modelBuilder
  .Entity<Blog>()
  .MapToStoredProcedures();
```  

<span data-ttu-id="2da50-109">這麼做會導致 Code First 使用部分慣例來建立預存程序，在資料庫中預期的圖形。</span><span class="sxs-lookup"><span data-stu-id="2da50-109">Doing this will cause Code First to use some conventions to build the expected shape of the stored procedures in the database.</span></span>  

- <span data-ttu-id="2da50-110">三個預存程序名為 **\<type_name\>（_i)**，  **\<type_name\>（_u)** 並 **\<type_名稱\>（_d)** （比方說，Blog_Insert、 Blog_Update 和 Blog_Delete）。</span><span class="sxs-lookup"><span data-stu-id="2da50-110">Three stored procedures named **\<type_name\>_Insert**, **\<type_name\>_Update** and **\<type_name\>_Delete** (for example, Blog_Insert, Blog_Update and Blog_Delete).</span></span>  
- <span data-ttu-id="2da50-111">參數名稱會對應到屬性名稱。</span><span class="sxs-lookup"><span data-stu-id="2da50-111">Parameter names correspond to the property names.</span></span>  
  > [!NOTE]
  > <span data-ttu-id="2da50-112">如果您使用 HasColumnName() 或資料行屬性來指定屬性資料行重新命名此名稱將用於參數，而不是屬性名稱。</span><span class="sxs-lookup"><span data-stu-id="2da50-112">If you use HasColumnName() or the Column attribute to rename the column for a given property then this name is used for parameters instead of the property name.</span></span>  
- <span data-ttu-id="2da50-113">**Insert 預存程序**會有每個屬性，除了標示為儲存所產生的參數 (身分識別或計算)。</span><span class="sxs-lookup"><span data-stu-id="2da50-113">**The insert stored procedure** will have a parameter for every property, except for those marked as store generated (identity or computed).</span></span> <span data-ttu-id="2da50-114">預存程序應該會傳回具有儲存所產生的每個屬性的資料行的結果集。</span><span class="sxs-lookup"><span data-stu-id="2da50-114">The stored procedure should return a result set with a column for each store generated property.</span></span>  
- <span data-ttu-id="2da50-115">**更新預存程序**會有每個屬性，除了以儲存所產生的模式 'Computed' 標記的參數。</span><span class="sxs-lookup"><span data-stu-id="2da50-115">**The update stored procedure** will have a parameter for every property, except for those marked with a store generated pattern of 'Computed'.</span></span> <span data-ttu-id="2da50-116">某些並行語彙基元需要原始值的參數，請參閱 <<c0>  *並行語彙基元*如需詳細資訊，如下一節。</span><span class="sxs-lookup"><span data-stu-id="2da50-116">Some concurrency tokens require a parameter for the original value, see the *Concurrency Tokens* section below for details.</span></span> <span data-ttu-id="2da50-117">預存程序應該傳回的結果集資料行的每個計算的屬性。</span><span class="sxs-lookup"><span data-stu-id="2da50-117">The stored procedure should return a result set with a column for each computed property.</span></span>  
- <span data-ttu-id="2da50-118">**刪除預存程序**應有的索引鍵值的實體 （或多個參數，如果實體有複合索引鍵） 的參數。</span><span class="sxs-lookup"><span data-stu-id="2da50-118">**The delete stored procedure** should have a parameter for the key value of the entity (or multiple parameters if the entity has a composite key).</span></span> <span data-ttu-id="2da50-119">此外，刪除程序也應具有參數的任何獨立關聯中的外部索引鍵會在目標資料表 （不需要對應外部索引鍵屬性宣告的實體中的關聯性）。</span><span class="sxs-lookup"><span data-stu-id="2da50-119">Additionally, the delete procedure should also have parameters for any independent association foreign keys on the target table (relationships that do not have corresponding foreign key properties declared in the entity).</span></span> <span data-ttu-id="2da50-120">某些並行語彙基元需要原始值的參數，請參閱 <<c0>  *並行語彙基元*如需詳細資訊，如下一節。</span><span class="sxs-lookup"><span data-stu-id="2da50-120">Some concurrency tokens require a parameter for the original value, see the *Concurrency Tokens* section below for details.</span></span>  

<span data-ttu-id="2da50-121">您可以使用下列類別做為範例：</span><span class="sxs-lookup"><span data-stu-id="2da50-121">Using the following class as an example:</span></span>  

``` csharp
public class Blog  
{  
  public int BlogId { get; set; }  
  public string Name { get; set; }  
  public string Url { get; set; }  
}
```  

<span data-ttu-id="2da50-122">預存程序就是預設值：</span><span class="sxs-lookup"><span data-stu-id="2da50-122">The default stored procedures would be:</span></span>  

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

### <a name="overriding-the-defaults"></a><span data-ttu-id="2da50-123">覆寫預設值</span><span class="sxs-lookup"><span data-stu-id="2da50-123">Overriding the Defaults</span></span>  

<span data-ttu-id="2da50-124">您可以覆寫部分或所有預設已設定的項目。</span><span class="sxs-lookup"><span data-stu-id="2da50-124">You can override part or all of what was configured by default.</span></span>  

<span data-ttu-id="2da50-125">您可以變更一或多個預存程序的名稱。</span><span class="sxs-lookup"><span data-stu-id="2da50-125">You can change the name of one or more stored procedures.</span></span> <span data-ttu-id="2da50-126">此範例中重新命名只更新預存程序。</span><span class="sxs-lookup"><span data-stu-id="2da50-126">This example renames the update stored procedure only.</span></span>  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.HasName("modify_blog")));
```  

<span data-ttu-id="2da50-127">此範例中重新命名所有的三個預存程序。</span><span class="sxs-lookup"><span data-stu-id="2da50-127">This example renames all three stored procedures.</span></span>  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.HasName("modify_blog"))  
     .Delete(d => d.HasName("delete_blog"))  
     .Insert(i => i.HasName("insert_blog")));
```  

<span data-ttu-id="2da50-128">在這些範例中，會呼叫鏈結在一起，但您也可以使用 lambda 區塊的語法。</span><span class="sxs-lookup"><span data-stu-id="2da50-128">In these examples the calls are chained together, but you can also use lambda block syntax.</span></span>  

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

<span data-ttu-id="2da50-129">此範例中重新命名 BlogId 屬性上更新預存程序的參數。</span><span class="sxs-lookup"><span data-stu-id="2da50-129">This example renames the parameter for the BlogId property on the update stored procedure.</span></span>  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.Parameter(b => b.BlogId, "blog_id")));
```  

<span data-ttu-id="2da50-130">這些呼叫都可鏈結且可組合。</span><span class="sxs-lookup"><span data-stu-id="2da50-130">These calls are all chainable and composable.</span></span> <span data-ttu-id="2da50-131">以下是重新命名所有的三個預存程序和其參數的範例。</span><span class="sxs-lookup"><span data-stu-id="2da50-131">Here is an example that renames all three stored procedures and their parameters.</span></span>  

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

<span data-ttu-id="2da50-132">您也可以變更中包含的資料庫產生值的結果集的資料行的名稱。</span><span class="sxs-lookup"><span data-stu-id="2da50-132">You can also change the name of the columns in the result set that contains database generated values.</span></span>  

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

## <a name="relationships-without-a-foreign-key-in-the-class-independent-associations"></a><span data-ttu-id="2da50-133">沒有類別 （獨立關聯） 中的外部索引鍵的關聯性</span><span class="sxs-lookup"><span data-stu-id="2da50-133">Relationships Without a Foreign Key in the Class (Independent Associations)</span></span>  

<span data-ttu-id="2da50-134">在類別定義中包含外部索引鍵屬性，則對應的參數可以重新命名為任何其他屬性相同的方式。</span><span class="sxs-lookup"><span data-stu-id="2da50-134">When a foreign key property is included in the class definition, the corresponding parameter can be renamed in the same way as any other property.</span></span> <span data-ttu-id="2da50-135">在類別中的外部索引鍵屬性不存在的關聯性，預設的參數名稱時，  **\<navigation_property_name\>_\<primary_key_name\>**。</span><span class="sxs-lookup"><span data-stu-id="2da50-135">When a relationship exists without a foreign key property in the class, the default parameter name is **\<navigation_property_name\>_\<primary_key_name\>**.</span></span>  

<span data-ttu-id="2da50-136">例如，下列類別定義會導致預存程序，來插入及更新的文章中所預期的 Blog_BlogId 參數。</span><span class="sxs-lookup"><span data-stu-id="2da50-136">For example, the following class definitions would result in a Blog_BlogId parameter being expected in the stored procedures to insert and update Posts.</span></span>  

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

### <a name="overriding-the-defaults"></a><span data-ttu-id="2da50-137">覆寫預設值</span><span class="sxs-lookup"><span data-stu-id="2da50-137">Overriding the Defaults</span></span>  

<span data-ttu-id="2da50-138">您可以變更參數所提供的參數方法的主索引鍵屬性的路徑不包含在類別中的外部索引鍵。</span><span class="sxs-lookup"><span data-stu-id="2da50-138">You can change parameters for foreign keys that are not included in the class by supplying the path to the primary key property to the Parameter method.</span></span>  

``` csharp
modelBuilder
  .Entity<Post>()  
  .MapToStoredProcedures(s =>  
    s.Insert(i => i.Parameter(p => p.Blog.BlogId, "blog_id")));
```  

<span data-ttu-id="2da50-139">如果您沒有相依的實體 （也就是導覽屬性</span><span class="sxs-lookup"><span data-stu-id="2da50-139">If you don’t have a navigation property on the dependent entity (i.e</span></span> <span data-ttu-id="2da50-140">沒有 Post.Blog 屬性） 則您可以使用關聯的方法來識別關聯性另一端，然後設定 參數對應至每個索引鍵的屬性。</span><span class="sxs-lookup"><span data-stu-id="2da50-140">no Post.Blog property) then you can use the Association method to identify the other end of the relationship and then configure the parameters that correspond to each of the key property(s).</span></span>  

``` csharp
modelBuilder
  .Entity<Post>()  
  .MapToStoredProcedures(s =>  
    s.Insert(i => i.Navigation<Blog>(  
      b => b.Posts,  
      c => c.Parameter(b => b.BlogId, "blog_id"))));
```  

## <a name="concurrency-tokens"></a><span data-ttu-id="2da50-141">並行語彙基元</span><span class="sxs-lookup"><span data-stu-id="2da50-141">Concurrency Tokens</span></span>  

<span data-ttu-id="2da50-142">Update 和 delete 預存程序可能也需要應付並行處理：</span><span class="sxs-lookup"><span data-stu-id="2da50-142">Update and delete stored procedures may also need to deal with concurrency:</span></span>  

- <span data-ttu-id="2da50-143">如果實體包含並行語彙基元，預存程序可以選擇性地具有輸出參數傳回的資料列更新/刪除 （受影響的資料列） 數目。</span><span class="sxs-lookup"><span data-stu-id="2da50-143">If the entity contains concurrency tokens, the stored procedure can optionally have an output parameter that returns the number of rows updated/deleted (rows affected).</span></span> <span data-ttu-id="2da50-144">這類參數必須設為使用 RowsAffectedParameter 方法。</span><span class="sxs-lookup"><span data-stu-id="2da50-144">Such a parameter must be configured using the RowsAffectedParameter method.</span></span>  
<span data-ttu-id="2da50-145">根據預設 EF 會使用 ExecuteNonQuery 的傳回值，來判斷多少資料列受到影響。</span><span class="sxs-lookup"><span data-stu-id="2da50-145">By default EF uses the return value from ExecuteNonQuery to determine how many rows were affected.</span></span> <span data-ttu-id="2da50-146">指定受影響的資料列的輸出參數會很有用，如果您執行的任何邏輯 ExecuteNonQuery 正在 （從 EF 的觀點來看） 不正確的傳回值會導致您預存程序中執行的結尾。</span><span class="sxs-lookup"><span data-stu-id="2da50-146">Specifying a rows affected output parameter is useful if you perform any logic in your sproc that would result in the return value of ExecuteNonQuery being incorrect (from EF's perspective) at the end of execution.</span></span>  
- <span data-ttu-id="2da50-147">針對每個並行有語彙基元會名為的參數 **\<property_name\>_Original** (比方說，Timestamp_Original)。</span><span class="sxs-lookup"><span data-stu-id="2da50-147">For each concurrency token there will be a parameter named **\<property_name\>_Original** (for example, Timestamp_Original ).</span></span> <span data-ttu-id="2da50-148">這會傳遞值時從資料庫查詢 – 此屬性的原始值。</span><span class="sxs-lookup"><span data-stu-id="2da50-148">This will be passed the original value of this property – the value when queried from the database.</span></span>  
    - <span data-ttu-id="2da50-149">並行語彙基元計算的資料集，例如時間戳記 – 只有原始的實值參數。</span><span class="sxs-lookup"><span data-stu-id="2da50-149">Concurrency tokens that are computed by the database – such as timestamps – will only have an original value parameter.</span></span>  
    - <span data-ttu-id="2da50-150">非計算的屬性設定為並行語彙基元也會更新程序中有新的值的參數。</span><span class="sxs-lookup"><span data-stu-id="2da50-150">Non-computed properties that are set as concurrency tokens will also have a parameter for the new value in the update procedure.</span></span> <span data-ttu-id="2da50-151">這會使用已經討論過的新值的命名慣例。</span><span class="sxs-lookup"><span data-stu-id="2da50-151">This uses the naming conventions already discussed for new values.</span></span> <span data-ttu-id="2da50-152">這類權杖的範例會為並行語彙基元使用部落格的 URL，因為這可由您的程式碼 （不同於這只會由資料庫更新的時間戳記語彙基元） 更新為新值，則需要新的值。</span><span class="sxs-lookup"><span data-stu-id="2da50-152">An example of such a token would be using a Blog's URL as a concurrency token, the new value is required because this can be updated to a new value by your code (unlike a Timestamp token which is only updated by the database).</span></span>  

<span data-ttu-id="2da50-153">這是範例類別，並更新預存程序具有時間戳記的並行語彙基元。</span><span class="sxs-lookup"><span data-stu-id="2da50-153">This is an example class and update stored procedure with a timestamp concurrency token.</span></span>  

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

<span data-ttu-id="2da50-154">以下是範例類別，並更新預存程序與非計算的並行語彙基元。</span><span class="sxs-lookup"><span data-stu-id="2da50-154">Here is an example class and update stored procedure with non-computed concurrency token.</span></span>  

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

### <a name="overriding-the-defaults"></a><span data-ttu-id="2da50-155">覆寫預設值</span><span class="sxs-lookup"><span data-stu-id="2da50-155">Overriding the Defaults</span></span>  

<span data-ttu-id="2da50-156">您可以選擇性地導入受影響的資料列參數。</span><span class="sxs-lookup"><span data-stu-id="2da50-156">You can optionally introduce a rows affected parameter.</span></span>  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.RowsAffectedParameter("rows_affected")));
```  

<span data-ttu-id="2da50-157">針對計算的資料庫並行語彙基元 – 其中只有原始的值會傳遞 – 您可以只使用標準的參數重新命名的機制來重新命名原始值的參數。</span><span class="sxs-lookup"><span data-stu-id="2da50-157">For database computed concurrency tokens – where only the original value is passed – you can just use the standard parameter renaming mechanism to rename the parameter for the original value.</span></span>  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.Parameter(b => b.Timestamp, "blog_timestamp")));
```  

<span data-ttu-id="2da50-158">針對非計算的並行語彙基元 – 其中同時的原始和新值會傳遞 – 您可以使用多載的參數，可讓您提供每個參數的名稱。</span><span class="sxs-lookup"><span data-stu-id="2da50-158">For non-computed concurrency tokens – where both the original and new value are passed – you can use an overload of Parameter that allows you to supply a name for each parameter.</span></span>  

``` csharp
modelBuilder
 .Entity<Blog>()
 .MapToStoredProcedures(s => s.Update(u => u.Parameter(b => b.Url, "blog_url", "blog_original_url")));
```  

## <a name="many-to-many-relationships"></a><span data-ttu-id="2da50-159">多對多關聯性</span><span class="sxs-lookup"><span data-stu-id="2da50-159">Many to Many Relationships</span></span>  

<span data-ttu-id="2da50-160">我們將使用下列類別做為範例，這一節。</span><span class="sxs-lookup"><span data-stu-id="2da50-160">We’ll use the following classes as an example in this section.</span></span>  

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

<span data-ttu-id="2da50-161">可以使用下列語法的預存程序對應多對多關聯性。</span><span class="sxs-lookup"><span data-stu-id="2da50-161">Many to many relationships can be mapped to stored procedures with the following syntax.</span></span>  

``` csharp
modelBuilder  
  .Entity<Post>()  
  .HasMany(p => p.Tags)  
  .WithMany(t => t.Posts)  
  .MapToStoredProcedures();
```  

<span data-ttu-id="2da50-162">如果沒有提供任何其他設定則會使用預設的下列預存程序的圖形。</span><span class="sxs-lookup"><span data-stu-id="2da50-162">If no other configuration is supplied then the following stored procedure shape is used by default.</span></span>  

- <span data-ttu-id="2da50-163">兩個預存程序名為 **\<type_one\>\<type_two\>（_i)** 並 **\<type_one\>\<type_two\>（_d)** （比方說，PostTag_Insert 和 PostTag_Delete）。</span><span class="sxs-lookup"><span data-stu-id="2da50-163">Two stored procedures named **\<type_one\>\<type_two\>_Insert** and **\<type_one\>\<type_two\>_Delete** (for example, PostTag_Insert and PostTag_Delete).</span></span>  
- <span data-ttu-id="2da50-164">參數會針對每個類型的索引鍵的值。</span><span class="sxs-lookup"><span data-stu-id="2da50-164">The parameters will be the key value(s) for each type.</span></span> <span data-ttu-id="2da50-165">之每個參數的名稱**\<type_name\>_\<property_name\>** （比方說，Post_PostId 和 Tag_TagId）。</span><span class="sxs-lookup"><span data-stu-id="2da50-165">The name of each parameter being **\<type_name\>_\<property_name\>** (for example, Post_PostId and Tag_TagId).</span></span>

<span data-ttu-id="2da50-166">以下是範例插入和更新預存程序。</span><span class="sxs-lookup"><span data-stu-id="2da50-166">Here are example insert and update stored procedures.</span></span>  

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

### <a name="overriding-the-defaults"></a><span data-ttu-id="2da50-167">覆寫預設值</span><span class="sxs-lookup"><span data-stu-id="2da50-167">Overriding the Defaults</span></span>  

<span data-ttu-id="2da50-168">類似的方式，來儲存實體的程序，可以設定的程序和參數名稱。</span><span class="sxs-lookup"><span data-stu-id="2da50-168">The procedure and parameter names can be configured in a similar way to entity stored procedures.</span></span>  

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
