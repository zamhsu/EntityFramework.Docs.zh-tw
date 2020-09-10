---
title: Code First Insert、Update 和 Delete 預存程式-EF6
description: Code First Entity Framework 6 中的插入、更新和刪除預存程式
author: divega
ms.date: 10/23/2016
ms.assetid: 9a7ae7f9-4072-4843-877d-506dd7eef576
uid: ef6/modeling/code-first/fluent/cud-stored-procedures
ms.openlocfilehash: 4db54d7199baa408017159e25ce79a9d70707c59
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618124"
---
# <a name="code-first-insert-update-and-delete-stored-procedures"></a><span data-ttu-id="8fbd1-103">Code First 插入、更新和刪除預存程式</span><span class="sxs-lookup"><span data-stu-id="8fbd1-103">Code First Insert, Update, and Delete Stored Procedures</span></span>
> [!NOTE]
> <span data-ttu-id="8fbd1-104">**僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。</span><span class="sxs-lookup"><span data-stu-id="8fbd1-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="8fbd1-105">如果您使用的是較早版本，則不適用部分或全部的資訊。</span><span class="sxs-lookup"><span data-stu-id="8fbd1-105">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="8fbd1-106">根據預設，Code First 會將所有實體設定為使用直接資料表存取來執行插入、更新和刪除命令。</span><span class="sxs-lookup"><span data-stu-id="8fbd1-106">By default, Code First will configure all entities to perform insert, update and delete commands using direct table access.</span></span> <span data-ttu-id="8fbd1-107">從 EF6 開始，您可以設定 Code First 模型，以針對模型中的部分或所有實體使用預存程式。</span><span class="sxs-lookup"><span data-stu-id="8fbd1-107">Starting in EF6 you can configure your Code First model to use stored procedures for some or all entities in your model.</span></span>  

## <a name="basic-entity-mapping"></a><span data-ttu-id="8fbd1-108">基底實體對應</span><span class="sxs-lookup"><span data-stu-id="8fbd1-108">Basic Entity Mapping</span></span>  

<span data-ttu-id="8fbd1-109">您可以使用流暢的 API，選擇使用預存程式進行插入、更新和刪除。</span><span class="sxs-lookup"><span data-stu-id="8fbd1-109">You can opt into using stored procedures for insert, update and delete using the Fluent API.</span></span>  

``` csharp
modelBuilder
  .Entity<Blog>()
  .MapToStoredProcedures();
```  

<span data-ttu-id="8fbd1-110">這麼做會導致 Code First 使用某些慣例，在資料庫中建立預存程式的預期圖形。</span><span class="sxs-lookup"><span data-stu-id="8fbd1-110">Doing this will cause Code First to use some conventions to build the expected shape of the stored procedures in the database.</span></span>  

- <span data-ttu-id="8fbd1-111">有三個名為\*\* \<type_name\> _Insert**、 \*\* \<type_name\> _Update**和\*\* \<type_name\> _Delete\*\* (的預存程式，例如 Blog_Insert、Blog_Update 和 Blog_Delete) 。</span><span class="sxs-lookup"><span data-stu-id="8fbd1-111">Three stored procedures named **\<type_name\>_Insert**, **\<type_name\>_Update** and **\<type_name\>_Delete** (for example, Blog_Insert, Blog_Update and Blog_Delete).</span></span>  
- <span data-ttu-id="8fbd1-112">參數名稱會對應至屬性名稱。</span><span class="sxs-lookup"><span data-stu-id="8fbd1-112">Parameter names correspond to the property names.</span></span>  
  > [!NOTE]
  > <span data-ttu-id="8fbd1-113">如果您使用 HasColumnName ( # A1 或資料行屬性來重新命名指定屬性的資料行，則此名稱會用於參數，而不是屬性名稱。</span><span class="sxs-lookup"><span data-stu-id="8fbd1-113">If you use HasColumnName() or the Column attribute to rename the column for a given property then this name is used for parameters instead of the property name.</span></span>  
- <span data-ttu-id="8fbd1-114">**插入預存** 程式將會有每個屬性的參數，但標示為存放區的 (識別或計算) 。</span><span class="sxs-lookup"><span data-stu-id="8fbd1-114">**The insert stored procedure** will have a parameter for every property, except for those marked as store generated (identity or computed).</span></span> <span data-ttu-id="8fbd1-115">預存程式應該會傳回結果集，其中包含每個存放區產生屬性的資料行。</span><span class="sxs-lookup"><span data-stu-id="8fbd1-115">The stored procedure should return a result set with a column for each store generated property.</span></span>  
- <span data-ttu-id="8fbd1-116">**更新預存** 程式將會有每個屬性的參數，但標有存放區產生之「計算」模式的標記除外。</span><span class="sxs-lookup"><span data-stu-id="8fbd1-116">**The update stored procedure** will have a parameter for every property, except for those marked with a store generated pattern of 'Computed'.</span></span> <span data-ttu-id="8fbd1-117">某些並行標記需要原始值的參數，請參閱下方的 *並行標記* 一節以取得詳細資料。</span><span class="sxs-lookup"><span data-stu-id="8fbd1-117">Some concurrency tokens require a parameter for the original value, see the *Concurrency Tokens* section below for details.</span></span> <span data-ttu-id="8fbd1-118">預存程式應該會傳回結果集，其中包含每個計算屬性的資料行。</span><span class="sxs-lookup"><span data-stu-id="8fbd1-118">The stored procedure should return a result set with a column for each computed property.</span></span>  
- <span data-ttu-id="8fbd1-119">如果實體有複合索引鍵) ，**刪除預存**程式應該要有實體 (或多個參數的索引鍵值參數。</span><span class="sxs-lookup"><span data-stu-id="8fbd1-119">**The delete stored procedure** should have a parameter for the key value of the entity (or multiple parameters if the entity has a composite key).</span></span> <span data-ttu-id="8fbd1-120">此外，在目標資料表上，delete 程式也應該有任何獨立關聯外鍵的參數 (不具有在實體) 中宣告之對應外鍵屬性的關聯性。</span><span class="sxs-lookup"><span data-stu-id="8fbd1-120">Additionally, the delete procedure should also have parameters for any independent association foreign keys on the target table (relationships that do not have corresponding foreign key properties declared in the entity).</span></span> <span data-ttu-id="8fbd1-121">某些並行標記需要原始值的參數，請參閱下方的 *並行標記* 一節以取得詳細資料。</span><span class="sxs-lookup"><span data-stu-id="8fbd1-121">Some concurrency tokens require a parameter for the original value, see the *Concurrency Tokens* section below for details.</span></span>  

<span data-ttu-id="8fbd1-122">使用下列類別做為範例：</span><span class="sxs-lookup"><span data-stu-id="8fbd1-122">Using the following class as an example:</span></span>  

``` csharp
public class Blog  
{  
  public int BlogId { get; set; }  
  public string Name { get; set; }  
  public string Url { get; set; }  
}
```  

<span data-ttu-id="8fbd1-123">預設的預存程式為：</span><span class="sxs-lookup"><span data-stu-id="8fbd1-123">The default stored procedures would be:</span></span>  

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

### <a name="overriding-the-defaults"></a><span data-ttu-id="8fbd1-124">覆寫預設值</span><span class="sxs-lookup"><span data-stu-id="8fbd1-124">Overriding the Defaults</span></span>  

<span data-ttu-id="8fbd1-125">您可以覆寫預設設定的部分或所有內容。</span><span class="sxs-lookup"><span data-stu-id="8fbd1-125">You can override part or all of what was configured by default.</span></span>  

<span data-ttu-id="8fbd1-126">您可以變更一個或多個預存程式的名稱。</span><span class="sxs-lookup"><span data-stu-id="8fbd1-126">You can change the name of one or more stored procedures.</span></span> <span data-ttu-id="8fbd1-127">此範例只會重新具名更新預存程式。</span><span class="sxs-lookup"><span data-stu-id="8fbd1-127">This example renames the update stored procedure only.</span></span>  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.HasName("modify_blog")));
```  

<span data-ttu-id="8fbd1-128">此範例會重新命名所有三個預存程式。</span><span class="sxs-lookup"><span data-stu-id="8fbd1-128">This example renames all three stored procedures.</span></span>  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.HasName("modify_blog"))  
     .Delete(d => d.HasName("delete_blog"))  
     .Insert(i => i.HasName("insert_blog")));
```  

<span data-ttu-id="8fbd1-129">在這些範例中，呼叫會連結在一起，但您也可以使用 lambda 區塊語法。</span><span class="sxs-lookup"><span data-stu-id="8fbd1-129">In these examples the calls are chained together, but you can also use lambda block syntax.</span></span>  

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

<span data-ttu-id="8fbd1-130">這個範例會重新具名更新預存程式上 BlogId 屬性的參數。</span><span class="sxs-lookup"><span data-stu-id="8fbd1-130">This example renames the parameter for the BlogId property on the update stored procedure.</span></span>  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.Parameter(b => b.BlogId, "blog_id")));
```  

<span data-ttu-id="8fbd1-131">這些呼叫都是可鏈結且可組合的。</span><span class="sxs-lookup"><span data-stu-id="8fbd1-131">These calls are all chainable and composable.</span></span> <span data-ttu-id="8fbd1-132">以下是重新命名所有三個預存程式及其參數的範例。</span><span class="sxs-lookup"><span data-stu-id="8fbd1-132">Here is an example that renames all three stored procedures and their parameters.</span></span>  

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

<span data-ttu-id="8fbd1-133">您也可以變更結果集中包含資料庫產生之值的資料行名稱。</span><span class="sxs-lookup"><span data-stu-id="8fbd1-133">You can also change the name of the columns in the result set that contains database generated values.</span></span>  

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

## <a name="relationships-without-a-foreign-key-in-the-class-independent-associations"></a><span data-ttu-id="8fbd1-134">類別中沒有外鍵的關聯性 (獨立關聯) </span><span class="sxs-lookup"><span data-stu-id="8fbd1-134">Relationships Without a Foreign Key in the Class (Independent Associations)</span></span>  

<span data-ttu-id="8fbd1-135">當類別定義中包含外鍵屬性時，可以使用與任何其他屬性相同的方式重新命名對應的參數。</span><span class="sxs-lookup"><span data-stu-id="8fbd1-135">When a foreign key property is included in the class definition, the corresponding parameter can be renamed in the same way as any other property.</span></span> <span data-ttu-id="8fbd1-136">當關聯性不存在類別中的外鍵屬性時，預設參數名稱為\*\* \<navigation_property_name\> _ \<primary_key_name\> \*\*。</span><span class="sxs-lookup"><span data-stu-id="8fbd1-136">When a relationship exists without a foreign key property in the class, the default parameter name is **\<navigation_property_name\>_\<primary_key_name\>**.</span></span>  

<span data-ttu-id="8fbd1-137">例如，下列類別定義會導致在預存程式中需要 Blog_BlogId 參數，以插入和更新貼文。</span><span class="sxs-lookup"><span data-stu-id="8fbd1-137">For example, the following class definitions would result in a Blog_BlogId parameter being expected in the stored procedures to insert and update Posts.</span></span>  

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

### <a name="overriding-the-defaults"></a><span data-ttu-id="8fbd1-138">覆寫預設值</span><span class="sxs-lookup"><span data-stu-id="8fbd1-138">Overriding the Defaults</span></span>  

<span data-ttu-id="8fbd1-139">您可以藉由提供主要索引鍵屬性的路徑給參數方法，來變更不包含在類別中的外鍵的參數。</span><span class="sxs-lookup"><span data-stu-id="8fbd1-139">You can change parameters for foreign keys that are not included in the class by supplying the path to the primary key property to the Parameter method.</span></span>  

``` csharp
modelBuilder
  .Entity<Post>()  
  .MapToStoredProcedures(s =>  
    s.Insert(i => i.Parameter(p => p.Blog.BlogId, "blog_id")));
```  

<span data-ttu-id="8fbd1-140">如果您沒有相依實體的導覽屬性 (即</span><span class="sxs-lookup"><span data-stu-id="8fbd1-140">If you don’t have a navigation property on the dependent entity (i.e</span></span> <span data-ttu-id="8fbd1-141">沒有 Post。 Blog 屬性) 接著您可以使用 Association 方法來識別關聯性的另一端，然後設定對應至每個索引鍵屬性 (s) 的參數。</span><span class="sxs-lookup"><span data-stu-id="8fbd1-141">no Post.Blog property) then you can use the Association method to identify the other end of the relationship and then configure the parameters that correspond to each of the key property(s).</span></span>  

``` csharp
modelBuilder
  .Entity<Post>()  
  .MapToStoredProcedures(s =>  
    s.Insert(i => i.Navigation<Blog>(  
      b => b.Posts,  
      c => c.Parameter(b => b.BlogId, "blog_id"))));
```  

## <a name="concurrency-tokens"></a><span data-ttu-id="8fbd1-142">並行語彙基元</span><span class="sxs-lookup"><span data-stu-id="8fbd1-142">Concurrency Tokens</span></span>  

<span data-ttu-id="8fbd1-143">更新和刪除預存程式可能也需要處理平行存取：</span><span class="sxs-lookup"><span data-stu-id="8fbd1-143">Update and delete stored procedures may also need to deal with concurrency:</span></span>  

- <span data-ttu-id="8fbd1-144">如果實體包含並行 token，則預存程式可以選擇性地有 output 參數，以傳回受影響) 的 (資料列更新/刪除的資料列數目。</span><span class="sxs-lookup"><span data-stu-id="8fbd1-144">If the entity contains concurrency tokens, the stored procedure can optionally have an output parameter that returns the number of rows updated/deleted (rows affected).</span></span> <span data-ttu-id="8fbd1-145">這類參數必須使用 RowsAffectedParameter 方法來設定。</span><span class="sxs-lookup"><span data-stu-id="8fbd1-145">Such a parameter must be configured using the RowsAffectedParameter method.</span></span>  
<span data-ttu-id="8fbd1-146">根據預設，EF 會使用來自 ExecuteNonQuery 的傳回值來判斷受影響的資料列數目。</span><span class="sxs-lookup"><span data-stu-id="8fbd1-146">By default EF uses the return value from ExecuteNonQuery to determine how many rows were affected.</span></span> <span data-ttu-id="8fbd1-147">如果您在您的執行程式中執行任何邏輯，會導致 ExecuteNonQuery 的傳回值不正確 (從 EF 在執行結束時) 的觀點，則指定受影響的資料列輸出參數會很有用。</span><span class="sxs-lookup"><span data-stu-id="8fbd1-147">Specifying a rows affected output parameter is useful if you perform any logic in your sproc that would result in the return value of ExecuteNonQuery being incorrect (from EF's perspective) at the end of execution.</span></span>  
- <span data-ttu-id="8fbd1-148">針對每個並行權杖，會有一個名為\*\* \<property_name\> _Original\*\*的參數 (例如 Timestamp_Original ) 。</span><span class="sxs-lookup"><span data-stu-id="8fbd1-148">For each concurrency token there will be a parameter named **\<property_name\>_Original** (for example, Timestamp_Original ).</span></span> <span data-ttu-id="8fbd1-149">這會傳遞這個屬性的原始值–從資料庫查詢時的值。</span><span class="sxs-lookup"><span data-stu-id="8fbd1-149">This will be passed the original value of this property – the value when queried from the database.</span></span>  
    - <span data-ttu-id="8fbd1-150">資料庫所計算的並行權杖（例如時間戳記）只會有原始值參數。</span><span class="sxs-lookup"><span data-stu-id="8fbd1-150">Concurrency tokens that are computed by the database – such as timestamps – will only have an original value parameter.</span></span>  
    - <span data-ttu-id="8fbd1-151">設定為並行標記的非計算屬性也會有 update 程式中新值的參數。</span><span class="sxs-lookup"><span data-stu-id="8fbd1-151">Non-computed properties that are set as concurrency tokens will also have a parameter for the new value in the update procedure.</span></span> <span data-ttu-id="8fbd1-152">這會使用已針對新值討論過的命名慣例。</span><span class="sxs-lookup"><span data-stu-id="8fbd1-152">This uses the naming conventions already discussed for new values.</span></span> <span data-ttu-id="8fbd1-153">這類權杖的其中一個範例是使用 Blog 的 URL 做為並行存取權杖，因為您的程式碼可以將其更新為新的值，所以需要新的值， (與只由資料庫) 更新的時間戳記權杖不同。</span><span class="sxs-lookup"><span data-stu-id="8fbd1-153">An example of such a token would be using a Blog's URL as a concurrency token, the new value is required because this can be updated to a new value by your code (unlike a Timestamp token which is only updated by the database).</span></span>  

<span data-ttu-id="8fbd1-154">這是範例類別，並以時間戳記並行標記更新預存程式。</span><span class="sxs-lookup"><span data-stu-id="8fbd1-154">This is an example class and update stored procedure with a timestamp concurrency token.</span></span>  

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

<span data-ttu-id="8fbd1-155">以下是範例類別，並使用非計算的並行標記更新預存程式。</span><span class="sxs-lookup"><span data-stu-id="8fbd1-155">Here is an example class and update stored procedure with non-computed concurrency token.</span></span>  

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

### <a name="overriding-the-defaults"></a><span data-ttu-id="8fbd1-156">覆寫預設值</span><span class="sxs-lookup"><span data-stu-id="8fbd1-156">Overriding the Defaults</span></span>  

<span data-ttu-id="8fbd1-157">您可以選擇性地引進受影響的資料列參數。</span><span class="sxs-lookup"><span data-stu-id="8fbd1-157">You can optionally introduce a rows affected parameter.</span></span>  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.RowsAffectedParameter("rows_affected")));
```  

<span data-ttu-id="8fbd1-158">針對資料庫計算的並行存取權杖（只有傳遞原始值的位置），您可以只使用標準參數重新命名機制來重新命名原始值的參數。</span><span class="sxs-lookup"><span data-stu-id="8fbd1-158">For database computed concurrency tokens – where only the original value is passed – you can just use the standard parameter renaming mechanism to rename the parameter for the original value.</span></span>  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.Parameter(b => b.Timestamp, "blog_timestamp")));
```  

<span data-ttu-id="8fbd1-159">針對非計算的並行存取權杖（同時傳遞原始值和新值），您可以使用參數的多載，讓您提供每個參數的名稱。</span><span class="sxs-lookup"><span data-stu-id="8fbd1-159">For non-computed concurrency tokens – where both the original and new value are passed – you can use an overload of Parameter that allows you to supply a name for each parameter.</span></span>  

``` csharp
modelBuilder
 .Entity<Blog>()
 .MapToStoredProcedures(s => s.Update(u => u.Parameter(b => b.Url, "blog_url", "blog_original_url")));
```  

## <a name="many-to-many-relationships"></a><span data-ttu-id="8fbd1-160">多對多關聯性</span><span class="sxs-lookup"><span data-stu-id="8fbd1-160">Many to Many Relationships</span></span>  

<span data-ttu-id="8fbd1-161">在本節中，我們將使用下列類別做為範例。</span><span class="sxs-lookup"><span data-stu-id="8fbd1-161">We’ll use the following classes as an example in this section.</span></span>  

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

<span data-ttu-id="8fbd1-162">您可以使用下列語法，將多對多關聯性對應至預存程式。</span><span class="sxs-lookup"><span data-stu-id="8fbd1-162">Many to many relationships can be mapped to stored procedures with the following syntax.</span></span>  

``` csharp
modelBuilder  
  .Entity<Post>()  
  .HasMany(p => p.Tags)  
  .WithMany(t => t.Posts)  
  .MapToStoredProcedures();
```  

<span data-ttu-id="8fbd1-163">如果未提供其他設定，則預設會使用下列預存程式圖形。</span><span class="sxs-lookup"><span data-stu-id="8fbd1-163">If no other configuration is supplied then the following stored procedure shape is used by default.</span></span>  

- <span data-ttu-id="8fbd1-164">有兩個名為\*\* \<type_one\> \<type_two\> _Insert**和** \<type_one\> \<type_two\> _Delete\*\* (的預存程式，例如 PostTag_Insert 和 PostTag_Delete) 。</span><span class="sxs-lookup"><span data-stu-id="8fbd1-164">Two stored procedures named **\<type_one\>\<type_two\>_Insert** and **\<type_one\>\<type_two\>_Delete** (for example, PostTag_Insert and PostTag_Delete).</span></span>  
- <span data-ttu-id="8fbd1-165">參數將會是每個類型)  (s 的索引鍵值。</span><span class="sxs-lookup"><span data-stu-id="8fbd1-165">The parameters will be the key value(s) for each type.</span></span> <span data-ttu-id="8fbd1-166">每個參數的名稱為\*\* \<type_name\> _ \<property_name\> \*\* (例如，Post_PostId 和 Tag_TagId) 。</span><span class="sxs-lookup"><span data-stu-id="8fbd1-166">The name of each parameter being **\<type_name\>_\<property_name\>** (for example, Post_PostId and Tag_TagId).</span></span>

<span data-ttu-id="8fbd1-167">以下是 insert 和 update 預存程式的範例。</span><span class="sxs-lookup"><span data-stu-id="8fbd1-167">Here are example insert and update stored procedures.</span></span>  

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

### <a name="overriding-the-defaults"></a><span data-ttu-id="8fbd1-168">覆寫預設值</span><span class="sxs-lookup"><span data-stu-id="8fbd1-168">Overriding the Defaults</span></span>  

<span data-ttu-id="8fbd1-169">您可以使用類似的方式，將程式和參數名稱設定為實體預存程式。</span><span class="sxs-lookup"><span data-stu-id="8fbd1-169">The procedure and parameter names can be configured in a similar way to entity stored procedures.</span></span>  

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
