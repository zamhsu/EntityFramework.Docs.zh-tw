---
title: 使用實體狀態-EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: acb27f46-3f3a-4179-874a-d6bea5d7120c
caps.latest.revision: 3
ms.openlocfilehash: e27d70dd7a6a63c2b225e9cf4fe7ac8cc280d4ca
ms.sourcegitcommit: f05e7b62584cf228f17390bb086a61d505712e1b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2018
ms.locfileid: "39120229"
---
# <a name="working-with-entity-states"></a><span data-ttu-id="c5b40-102">使用實體狀態</span><span class="sxs-lookup"><span data-stu-id="c5b40-102">Working with entity states</span></span>
<span data-ttu-id="c5b40-103">本主題會討論如何加入及附加實體內容和 Entity Framework 如何處理這些 SaveChanges 期間。</span><span class="sxs-lookup"><span data-stu-id="c5b40-103">This topic will cover how to add and attach entities to a context and how Entity Framework processes these during SaveChanges.</span></span>
<span data-ttu-id="c5b40-104">Entity Framework 會負責的追蹤實體時連線到內容中，但在中斷連線 」 或 「 多層式架構的情況下，您可以讓 EF 知道何種狀態，您的實體的狀態應該是在。</span><span class="sxs-lookup"><span data-stu-id="c5b40-104">Entity Framework takes care of tracking the state of entities while they are connected to a context, but in disconnected or N-Tier scenarios you can let EF know what state your entities should be in.</span></span>
<span data-ttu-id="c5b40-105">本主題所示範的技巧同樣適用於使用 Code First 和 EF 設計工具所建立的模型。</span><span class="sxs-lookup"><span data-stu-id="c5b40-105">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

## <a name="entity-states-and-savechanges"></a><span data-ttu-id="c5b40-106">實體狀態和 SaveChanges</span><span class="sxs-lookup"><span data-stu-id="c5b40-106">Entity states and SaveChanges</span></span>

<span data-ttu-id="c5b40-107">EntityState 列舉所定義時，實體可以在五種狀態之一。</span><span class="sxs-lookup"><span data-stu-id="c5b40-107">An entity can be in one of five states as defined by the EntityState enumeration.</span></span> <span data-ttu-id="c5b40-108">這些狀態包括：</span><span class="sxs-lookup"><span data-stu-id="c5b40-108">These states are:</span></span>  

- <span data-ttu-id="c5b40-109">新增： 實體內容正在追蹤，但尚未存在資料庫中</span><span class="sxs-lookup"><span data-stu-id="c5b40-109">Added: the entity is being tracked by the context but does not yet exist in the database</span></span>  
- <span data-ttu-id="c5b40-110">Unchanged： 實體內容正在追蹤，且存在於資料庫中，並從資料庫中的值未變更其屬性值</span><span class="sxs-lookup"><span data-stu-id="c5b40-110">Unchanged: the entity is being tracked by the context and exists in the database, and its property values have not changed from the values in the database</span></span>  
- <span data-ttu-id="c5b40-111">修改過： 實體內容正在追蹤存在於資料庫中，並修改部份或全部的屬性值</span><span class="sxs-lookup"><span data-stu-id="c5b40-111">Modified: the entity is being tracked by the context and exists in the database, and some or all of its property values have been modified</span></span>  
- <span data-ttu-id="c5b40-112">已刪除： 實體內容正在追蹤和存在於資料庫中，但已標記為要在下一次，好讓呼叫 SaveChanges 從資料庫刪除</span><span class="sxs-lookup"><span data-stu-id="c5b40-112">Deleted: the entity is being tracked by the context and exists in the database, but has been marked for deletion from the database the next time SaveChanges is called</span></span>  
- <span data-ttu-id="c5b40-113">中斷連結： 實體沒有追蹤內容</span><span class="sxs-lookup"><span data-stu-id="c5b40-113">Detached: the entity is not being tracked by the context</span></span>  

<span data-ttu-id="c5b40-114">SaveChanges 會執行不同的項目，實體在不同的狀態：</span><span class="sxs-lookup"><span data-stu-id="c5b40-114">SaveChanges does different things for entities in different states:</span></span>  

- <span data-ttu-id="c5b40-115">未變更的實體不會觸及的 SaveChanges。</span><span class="sxs-lookup"><span data-stu-id="c5b40-115">Unchanged entities are not touched by SaveChanges.</span></span> <span data-ttu-id="c5b40-116">更新不會傳送到在 Unchanged 狀態的實體資料庫。</span><span class="sxs-lookup"><span data-stu-id="c5b40-116">Updates are not sent to the database for entities in the Unchanged state.</span></span>  
- <span data-ttu-id="c5b40-117">加入實體插入至資料庫，然後變得不變時 SaveChanges 傳回。</span><span class="sxs-lookup"><span data-stu-id="c5b40-117">Added entities are inserted into the database and then become Unchanged when SaveChanges returns.</span></span>  
- <span data-ttu-id="c5b40-118">已修改之實體資料庫中會更新，並接著會保持不變時 SaveChanges 傳回。</span><span class="sxs-lookup"><span data-stu-id="c5b40-118">Modified entities are updated in the database and then become Unchanged when SaveChanges returns.</span></span>  
- <span data-ttu-id="c5b40-119">已刪除的實體會從資料庫刪除，然後從內容中斷連結。</span><span class="sxs-lookup"><span data-stu-id="c5b40-119">Deleted entities are deleted from the database and are then detached from the context.</span></span>  

<span data-ttu-id="c5b40-120">下列範例顯示實體或實體圖形的狀態可變更所在的方式。</span><span class="sxs-lookup"><span data-stu-id="c5b40-120">The following examples show ways in which the state of an entity or an entity graph can be changed.</span></span>  

## <a name="adding-a-new-entity-to-the-context"></a><span data-ttu-id="c5b40-121">將新實體新增至內容</span><span class="sxs-lookup"><span data-stu-id="c5b40-121">Adding a new entity to the context</span></span>  

<span data-ttu-id="c5b40-122">可以 DbSet 上呼叫 Add 方法，將新實體新增至內容。</span><span class="sxs-lookup"><span data-stu-id="c5b40-122">A new entity can be added to the context by calling the Add method on DbSet.</span></span>
<span data-ttu-id="c5b40-123">這會讓實體進入 Added 狀態中，這表示，它會插入至資料庫下一次，好讓呼叫 SaveChanges。</span><span class="sxs-lookup"><span data-stu-id="c5b40-123">This puts the entity into the Added state, meaning that it will be inserted into the database the next time that SaveChanges is called.</span></span>
<span data-ttu-id="c5b40-124">例如: </span><span class="sxs-lookup"><span data-stu-id="c5b40-124">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = new Blog { Name = "ADO.NET Blog" };
    context.Blogs.Add(blog);
    context.SaveChanges();
}
```  

<span data-ttu-id="c5b40-125">將新實體新增至內容的另一個方法是將其狀態變更為 Added。</span><span class="sxs-lookup"><span data-stu-id="c5b40-125">Another way to add a new entity to the context is to change its state to Added.</span></span> <span data-ttu-id="c5b40-126">例如: </span><span class="sxs-lookup"><span data-stu-id="c5b40-126">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = new Blog { Name = "ADO.NET Blog" };
    context.Entry(blog).State = EntityState.Added;
    context.SaveChanges();
}
```  

<span data-ttu-id="c5b40-127">最後，您也可以以它連結到另一個已追蹤的實體，以新增新的實體內容。</span><span class="sxs-lookup"><span data-stu-id="c5b40-127">Finally, you can add a new entity to the context by hooking it up to another entity that is already being tracked.</span></span>
<span data-ttu-id="c5b40-128">這可能是藉由將新的實體加入至另一個實體的集合導覽屬性或設定以指向新的實體的另一個實體的參考導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="c5b40-128">This could be by adding the new entity to the collection navigation property of another entity or by setting a reference navigation property of another entity to point to the new entity.</span></span> <span data-ttu-id="c5b40-129">例如: </span><span class="sxs-lookup"><span data-stu-id="c5b40-129">For example:</span></span>  

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

<span data-ttu-id="c5b40-130">請注意，所有的這些範例中所加入的實體參考不是其他實體有尚未追蹤然後這些新的實體也會加入至內容，並將插入至資料庫下一次，好讓呼叫 SaveChanges。</span><span class="sxs-lookup"><span data-stu-id="c5b40-130">Note that for all of these examples if the entity being added has references to other entities that are not yet tracked then these new entities will also be added to the context and will be inserted into the database the next time that SaveChanges is called.</span></span>  

## <a name="attaching-an-existing-entity-to-the-context"></a><span data-ttu-id="c5b40-131">將現有實體附加至內容</span><span class="sxs-lookup"><span data-stu-id="c5b40-131">Attaching an existing entity to the context</span></span>  

<span data-ttu-id="c5b40-132">如果您有一個實體，您已經知道存在於資料庫，但其中沒有目前追蹤內容您可以指示要追蹤的實體上 DbSet 使用附加方法的內容。</span><span class="sxs-lookup"><span data-stu-id="c5b40-132">If you have an entity that you know already exists in the database but which is not currently being tracked by the context then you can tell the context to track the entity using the Attach method on DbSet.</span></span> <span data-ttu-id="c5b40-133">實體會處於 Unchanged 狀態的內容中。</span><span class="sxs-lookup"><span data-stu-id="c5b40-133">The entity will be in the Unchanged state in the context.</span></span> <span data-ttu-id="c5b40-134">例如: </span><span class="sxs-lookup"><span data-stu-id="c5b40-134">For example:</span></span>  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Blogs.Attach(existingBlog);

    // Do some more work...  

    context.SaveChanges();
}
```  

<span data-ttu-id="c5b40-135">請注意資料庫會進行的任何變更，是否呼叫 SaveChanges 時沒有進行任何其他操作的附加的實體。</span><span class="sxs-lookup"><span data-stu-id="c5b40-135">Note that no changes will be made to the database if SaveChanges is called without doing any other manipulation of the attached entity.</span></span> <span data-ttu-id="c5b40-136">這是因為實體處於 Unchanged 狀態。</span><span class="sxs-lookup"><span data-stu-id="c5b40-136">This is because the entity is in the Unchanged state.</span></span>  

<span data-ttu-id="c5b40-137">若要將現有實體附加至內容的另一個方法是將其狀態變更為未變更。</span><span class="sxs-lookup"><span data-stu-id="c5b40-137">Another way to attach an existing entity to the context is to change its state to Unchanged.</span></span> <span data-ttu-id="c5b40-138">例如: </span><span class="sxs-lookup"><span data-stu-id="c5b40-138">For example:</span></span>  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Entry(existingBlog).State = EntityState.Unchanged;

    // Do some more work...  

    context.SaveChanges();
}
```  

<span data-ttu-id="c5b40-139">請注意，針對這兩個範例所附加的實體是否還不能追蹤其他實體的參考然後這些新的實體也附加到在 Unchanged 狀態的內容。</span><span class="sxs-lookup"><span data-stu-id="c5b40-139">Note that for both of these examples if the entity being attached has references to other entities that are not yet tracked then these new entities will also attached to the context in the Unchanged state.</span></span>  

## <a name="attaching-an-existing-but-modified-entity-to-the-context"></a><span data-ttu-id="c5b40-140">附加現有但內容已修改的實體</span><span class="sxs-lookup"><span data-stu-id="c5b40-140">Attaching an existing but modified entity to the context</span></span>  

<span data-ttu-id="c5b40-141">如果您有一個實體，您已經知道存在於資料庫中，但變更可能已進行，您可以指示要附加的實體，並將其狀態設定為已修改的內容。</span><span class="sxs-lookup"><span data-stu-id="c5b40-141">If you have an entity that you know already exists in the database but to which changes may have been made then you can tell the context to attach the entity and set its state to Modified.</span></span>
<span data-ttu-id="c5b40-142">例如: </span><span class="sxs-lookup"><span data-stu-id="c5b40-142">For example:</span></span>  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Entry(existingBlog).State = EntityState.Modified;

    // Do some more work...  

    context.SaveChanges();
}
```  

<span data-ttu-id="c5b40-143">當您將狀態變更為已修改之實體的所有屬性都將都標示為已修改，所有屬性值將會都傳送至資料庫，讓呼叫 SaveChanges 時。</span><span class="sxs-lookup"><span data-stu-id="c5b40-143">When you change the state to Modified all the properties of the entity will be marked as modified and all the property values will be sent to the database when SaveChanges is called.</span></span>  

<span data-ttu-id="c5b40-144">請注意，是否所附加的實體會不追蹤其他實體的參考，這些新的實體會附加到在 Unchanged 狀態的內容，它們就不會自動進行修改。</span><span class="sxs-lookup"><span data-stu-id="c5b40-144">Note that if the entity being attached has references to other entities that are not yet tracked, then these new entities will attached to the context in the Unchanged state—they will not automatically be made Modified.</span></span>
<span data-ttu-id="c5b40-145">如果您有多個要標示為已修改的實體您應該針對每一個實體的個別設定的狀態。</span><span class="sxs-lookup"><span data-stu-id="c5b40-145">If you have multiple entities that need to be marked Modified you should set the state for each of these entities individually.</span></span>  

## <a name="changing-the-state-of-a-tracked-entity"></a><span data-ttu-id="c5b40-146">變更追蹤的實體狀態</span><span class="sxs-lookup"><span data-stu-id="c5b40-146">Changing the state of a tracked entity</span></span>  

<span data-ttu-id="c5b40-147">您可以變更其項目上設定 [狀態] 屬性已在追蹤實體的狀態。</span><span class="sxs-lookup"><span data-stu-id="c5b40-147">You can change the state of an entity that is already being tracked by setting the State property on its entry.</span></span> <span data-ttu-id="c5b40-148">例如: </span><span class="sxs-lookup"><span data-stu-id="c5b40-148">For example:</span></span>  

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

<span data-ttu-id="c5b40-149">請注意，呼叫 新增或附加已追蹤的實體也可用來將實體狀態變更。</span><span class="sxs-lookup"><span data-stu-id="c5b40-149">Note that calling Add or Attach for an entity that is already tracked can also be used to change the entity state.</span></span> <span data-ttu-id="c5b40-150">比方說，如果呼叫附加目前處於 Added 狀態的實體，將可以變更其狀態為 Unchanged。</span><span class="sxs-lookup"><span data-stu-id="c5b40-150">For example, calling Attach for an entity that is currently in the Added state will change its state to Unchanged.</span></span>  

## <a name="insert-or-update-pattern"></a><span data-ttu-id="c5b40-151">插入或更新模式</span><span class="sxs-lookup"><span data-stu-id="c5b40-151">Insert or update pattern</span></span>  

<span data-ttu-id="c5b40-152">有些應用程式的常見模式是將實體新增為新 （導致資料庫的插入），或附加為現有的實體，並標示為已修改 （導致資料庫更新） 的主索引鍵的值而定。</span><span class="sxs-lookup"><span data-stu-id="c5b40-152">A common pattern for some applications is to either Add an entity as new (resulting in a database insert) or Attach an entity as existing and mark it as modified (resulting in a database update) depending on the value of the primary key.</span></span>
<span data-ttu-id="c5b40-153">比方說，使用資料庫產生的整數主索引鍵時很常見，若要將新的零索引鍵的實體和具有與現有的非零索引鍵的實體。</span><span class="sxs-lookup"><span data-stu-id="c5b40-153">For example, when using database generated integer primary keys it is common to treat an entity with a zero key as new and an entity with a non-zero key as existing.</span></span>
<span data-ttu-id="c5b40-154">此模式可藉由設定主索引鍵值的核取為基礎的實體狀態。</span><span class="sxs-lookup"><span data-stu-id="c5b40-154">This pattern can be achieved by setting the entity state based on a check of the primary key value.</span></span> <span data-ttu-id="c5b40-155">例如: </span><span class="sxs-lookup"><span data-stu-id="c5b40-155">For example:</span></span>  

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

<span data-ttu-id="c5b40-156">請注意，當您將狀態變更為已修改之實體的所有屬性都將都標示為已修改的所有屬性值將會都傳送至資料庫，讓呼叫 SaveChanges 時。</span><span class="sxs-lookup"><span data-stu-id="c5b40-156">Note that when you change the state to Modified all the properties of the entity will be marked as modified and all the property values will be sent to the database when SaveChanges is called.</span></span>  
