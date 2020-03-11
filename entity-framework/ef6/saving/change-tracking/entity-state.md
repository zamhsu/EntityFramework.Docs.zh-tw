---
title: 使用實體狀態-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: acb27f46-3f3a-4179-874a-d6bea5d7120c
ms.openlocfilehash: ef0e8d5a5a9d66adab7046088c49d8cd472edc8a
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78419696"
---
# <a name="working-with-entity-states"></a>使用實體狀態
本主題將說明如何將實體加入和附加至內容，以及 Entity Framework 如何在 SaveChanges 期間處理這些專案。
Entity Framework 會負責追蹤實體連接到內容時的狀態，但是在中斷連線或多層式案例中，您可以讓 EF 知道您的實體應該位於哪一個狀態。
本主題所示範的技巧同樣適用於使用 Code First 和 EF 設計工具所建立的模型。  

## <a name="entity-states-and-savechanges"></a>實體狀態和 SaveChanges

實體可以是 EntityState 列舉所定義的五種狀態之一。 這些狀態包括：  

- 已加入：實體正在由內容追蹤，但尚未存在於資料庫中  
- 未變更：實體正在由內容追蹤，而且存在於資料庫中，而且其屬性值尚未從資料庫中的值變更  
- 已修改：實體正在由內容追蹤，而且存在於資料庫中，而且部分或全部的屬性值已修改  
- 已刪除：實體正在由內容追蹤，而且存在於資料庫中，但在下一次呼叫 SaveChanges 時，已標記為要從資料庫中刪除  
- 已卸離：實體未由內容追蹤  

SaveChanges 會針對不同狀態的實體執行不同的動作：  

- SaveChanges 不觸及未變更的實體。 對於處於未變更狀態的實體，不會將更新傳送至資料庫。  
- 新增的實體會插入至資料庫，然後在 SaveChanges 傳回時變得不變。  
- 修改過的實體會在資料庫中更新，然後在 SaveChanges 傳回時變得不變。  
- 已刪除的實體會從資料庫中刪除，然後從內容卸離。  

下列範例會顯示實體或實體圖形的狀態可以變更的方式。  

## <a name="adding-a-new-entity-to-the-context"></a>將新實體新增至內容  

可以藉由在 DbSet 上呼叫 Add 方法，將新的實體加入至內容中。
這會讓實體進入已加入的狀態，這表示它會在下次呼叫 SaveChanges 時插入至資料庫。
例如：  

``` csharp
using (var context = new BloggingContext())
{
    var blog = new Blog { Name = "ADO.NET Blog" };
    context.Blogs.Add(blog);
    context.SaveChanges();
}
```  

另一個將新實體新增至內容的方法，是將其狀態變更為 [已新增]。 例如：  

``` csharp
using (var context = new BloggingContext())
{
    var blog = new Blog { Name = "ADO.NET Blog" };
    context.Entry(blog).State = EntityState.Added;
    context.SaveChanges();
}
```  

最後，您可以將新實體連結至內容，方法是將它連結到已追蹤的另一個實體。
這可能是藉由將新實體新增至另一個實體的集合導覽屬性，或設定另一個實體的參考導覽屬性來指向新的實體。 例如：  

``` csharp
using (var context = new BloggingContext())
{
    // Add a new User by setting a reference from a tracked Blog
    var blog = context.Blogs.Find(1);
    blog.Owner = new User { UserName = "johndoe1987" };

    // Add a new Post by adding to the collection of a tracked Blog
    blog.Posts.Add(new Post { Name = "How to Add Entities" });

    context.SaveChanges();
}
```  

請注意，在這些範例中，如果要加入的實體具有尚未追蹤之其他實體的參考，則這些新的實體也會加入至內容中，並在下次呼叫 SaveChanges 時插入資料庫。  

## <a name="attaching-an-existing-entity-to-the-context"></a>將現有的實體附加至內容  

如果您的實體已經存在於資料庫中，但目前未由內容追蹤，您可以使用 DbSet 上的 Attach 方法，告訴內容追蹤該實體。 實體在內容中的狀態會是 [未變更]。 例如：  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Blogs.Attach(existingBlog);

    // Do some more work...  

    context.SaveChanges();
}
```  

請注意，如果呼叫 SaveChanges，而不進行任何其他的附加實體操作，則不會對資料庫進行任何變更。 這是因為實體處於未變更狀態。  

另一個將現有實體附加至內容的方法，是將其狀態變更為 [未變更]。 例如：  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Entry(existingBlog).State = EntityState.Unchanged;

    // Do some more work...  

    context.SaveChanges();
}
```  

請注意，在這兩個範例中，如果附加的實體具有尚未追蹤之其他實體的參考，則這些新的實體也會附加至未變更狀態的內容。  

## <a name="attaching-an-existing-but-modified-entity-to-the-context"></a>將現有但已修改的實體附加至內容  

如果您的實體已經存在於資料庫中，但可能已進行變更，則可以告訴內容附加實體，並將其狀態設定為 [已修改]。
例如：  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Entry(existingBlog).State = EntityState.Modified;

    // Do some more work...  

    context.SaveChanges();
}
```  

當您變更狀態以修改時，實體的所有屬性都會標示為已修改，而且在呼叫 SaveChanges 時，所有屬性值都會傳送至資料庫。  

請注意，如果所附加的實體具有其他尚未追蹤之實體的參考，則這些新的實體會附加至未變更狀態的內容，而不會自動進行修改。
如果您有多個需要標示為已修改的實體，您應該個別設定這些實體的狀態。  

## <a name="changing-the-state-of-a-tracked-entity"></a>變更已追蹤實體的狀態  

您可以藉由在其專案上設定 State 屬性，來變更已追蹤之實體的狀態。 例如：  

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

請注意，針對已追蹤的實體呼叫 [加入] 或 [附加] 也可以用來變更實體狀態。 例如，針對目前處於已加入狀態的實體呼叫 Attach，會將其狀態變更為不變。  

## <a name="insert-or-update-pattern"></a>插入或更新模式  

某些應用程式的常見模式是將實體新增為新的（產生資料庫插入），或將實體附加為現有的，並將它標示為已修改（產生資料庫更新），視主鍵的值而定。
例如，當使用資料庫產生的整數主鍵時，通常會將具有零索引鍵的實體視為新，並將具有非零索引鍵的實體當做現有的。
您可以根據主鍵值的檢查設定實體狀態來達成此模式。 例如：  

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

請注意，當您變更狀態以修改時，實體的所有屬性都會標示為已修改，而且在呼叫 SaveChanges 時，所有屬性值都會傳送至資料庫。  
