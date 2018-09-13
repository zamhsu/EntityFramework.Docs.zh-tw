---
title: 使用實體狀態-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: acb27f46-3f3a-4179-874a-d6bea5d7120c
ms.openlocfilehash: c1dde7810d1dfa8a73e6bd2cf091b24be6b865d8
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2018
ms.locfileid: "45490639"
---
# <a name="working-with-entity-states"></a>使用實體狀態
本主題會討論如何加入及附加實體內容和 Entity Framework 如何處理這些 SaveChanges 期間。
Entity Framework 會負責的追蹤實體時連線到內容中，但在中斷連線 」 或 「 多層式架構的情況下，您可以讓 EF 知道何種狀態，您的實體的狀態應該是在。
本主題所示範的技巧同樣適用於使用 Code First 和 EF 設計工具所建立的模型。  

## <a name="entity-states-and-savechanges"></a>實體狀態和 SaveChanges

EntityState 列舉所定義時，實體可以在五種狀態之一。 這些狀態包括：  

- 新增： 實體內容正在追蹤，但尚未存在資料庫中  
- Unchanged： 實體內容正在追蹤，且存在於資料庫中，並從資料庫中的值未變更其屬性值  
- 修改過： 實體內容正在追蹤存在於資料庫中，並修改部份或全部的屬性值  
- 已刪除： 實體內容正在追蹤和存在於資料庫中，但已標記為要在下一次，好讓呼叫 SaveChanges 從資料庫刪除  
- 中斷連結： 實體沒有追蹤內容  

SaveChanges 會執行不同的項目，實體在不同的狀態：  

- 未變更的實體不會觸及的 SaveChanges。 更新不會傳送到在 Unchanged 狀態的實體資料庫。  
- 加入實體插入至資料庫，然後變得不變時 SaveChanges 傳回。  
- 已修改之實體資料庫中會更新，並接著會保持不變時 SaveChanges 傳回。  
- 已刪除的實體會從資料庫刪除，然後從內容中斷連結。  

下列範例顯示實體或實體圖形的狀態可變更所在的方式。  

## <a name="adding-a-new-entity-to-the-context"></a>將新實體新增至內容  

可以 DbSet 上呼叫 Add 方法，將新實體新增至內容。
這會讓實體進入 Added 狀態中，這表示，它會插入至資料庫下一次，好讓呼叫 SaveChanges。
例如:   

``` csharp
using (var context = new BloggingContext())
{
    var blog = new Blog { Name = "ADO.NET Blog" };
    context.Blogs.Add(blog);
    context.SaveChanges();
}
```  

將新實體新增至內容的另一個方法是將其狀態變更為 Added。 例如:   

``` csharp
using (var context = new BloggingContext())
{
    var blog = new Blog { Name = "ADO.NET Blog" };
    context.Entry(blog).State = EntityState.Added;
    context.SaveChanges();
}
```  

最後，您也可以以它連結到另一個已追蹤的實體，以新增新的實體內容。
這可能是藉由將新的實體加入至另一個實體的集合導覽屬性或設定以指向新的實體的另一個實體的參考導覽屬性。 例如:   

``` csharp
using (var context = new BloggingContext())
{
    // Add a new User by setting a reference from a tracked Blog
    var blog = context.Blogs.Find(1);
    blog.Owner = new User { UserName = "johndoe1987" };

    // Add a new Post by adding to the collection of a tracked Blog
    var blog = context.Blogs.Find(2);
    blog.Posts.Add(new Post { Name = "How to Add Entities" });

    context.SaveChanges();
}
```  

請注意，所有的這些範例中所加入的實體參考不是其他實體有尚未追蹤然後這些新的實體也會加入至內容，並將插入至資料庫下一次，好讓呼叫 SaveChanges。  

## <a name="attaching-an-existing-entity-to-the-context"></a>將現有實體附加至內容  

如果您有一個實體，您已經知道存在於資料庫，但其中沒有目前追蹤內容您可以指示要追蹤的實體上 DbSet 使用附加方法的內容。 實體會處於 Unchanged 狀態的內容中。 例如:   

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Blogs.Attach(existingBlog);

    // Do some more work...  

    context.SaveChanges();
}
```  

請注意資料庫會進行的任何變更，是否呼叫 SaveChanges 時沒有進行任何其他操作的附加的實體。 這是因為實體處於 Unchanged 狀態。  

若要將現有實體附加至內容的另一個方法是將其狀態變更為未變更。 例如:   

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Entry(existingBlog).State = EntityState.Unchanged;

    // Do some more work...  

    context.SaveChanges();
}
```  

請注意，針對這兩個範例所附加的實體是否還不能追蹤其他實體的參考然後這些新的實體也附加到在 Unchanged 狀態的內容。  

## <a name="attaching-an-existing-but-modified-entity-to-the-context"></a>附加現有但內容已修改的實體  

如果您有一個實體，您已經知道存在於資料庫中，但變更可能已進行，您可以指示要附加的實體，並將其狀態設定為已修改的內容。
例如:   

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Entry(existingBlog).State = EntityState.Modified;

    // Do some more work...  

    context.SaveChanges();
}
```  

當您將狀態變更為已修改之實體的所有屬性都將都標示為已修改，所有屬性值將會都傳送至資料庫，讓呼叫 SaveChanges 時。  

請注意，是否所附加的實體會不追蹤其他實體的參考，這些新的實體會附加到在 Unchanged 狀態的內容，它們就不會自動進行修改。
如果您有多個要標示為已修改的實體您應該針對每一個實體的個別設定的狀態。  

## <a name="changing-the-state-of-a-tracked-entity"></a>變更追蹤的實體狀態  

您可以變更其項目上設定 [狀態] 屬性已在追蹤實體的狀態。 例如:   

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Blogs.Attach(existingBlog);
    context.Entry(existingBlog).State = EntityState.Unchanged;

    // Do some more work...  

    context.SaveChanges();
}
```  

請注意，呼叫 新增或附加已追蹤的實體也可用來將實體狀態變更。 比方說，如果呼叫附加目前處於 Added 狀態的實體，將可以變更其狀態為 Unchanged。  

## <a name="insert-or-update-pattern"></a>插入或更新模式  

有些應用程式的常見模式是將實體新增為新 （導致資料庫的插入），或附加為現有的實體，並標示為已修改 （導致資料庫更新） 的主索引鍵的值而定。
比方說，使用資料庫產生的整數主索引鍵時很常見，若要將新的零索引鍵的實體和具有與現有的非零索引鍵的實體。
此模式可藉由設定主索引鍵值的核取為基礎的實體狀態。 例如:   

``` csharp
public void InsertOrUpdate(Blog blog)
{
    using (var context = new BloggingContext())
    {
        context.Entry(blog).State = blog.BlogId == 0 ?
                                   EntityState.Added :
                                   EntityState.Modified;

        context.SaveChanges();
    }
}
```  

請注意，當您將狀態變更為已修改之實體的所有屬性都將都標示為已修改的所有屬性值將會都傳送至資料庫，讓呼叫 SaveChanges 時。  
