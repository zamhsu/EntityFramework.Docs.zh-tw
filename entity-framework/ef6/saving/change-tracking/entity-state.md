---
title: 使用實體狀態-EF6
description: 使用 Entity Framework 6 中的實體狀態
author: divega
ms.date: 10/23/2016
ms.assetid: acb27f46-3f3a-4179-874a-d6bea5d7120c
uid: ef6/saving/change-tracking/entity-state
ms.openlocfilehash: 981bdbca982403338f3f65a41f601641d59d74d8
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619979"
---
# <a name="working-with-entity-states"></a><span data-ttu-id="ae166-103">使用實體狀態</span><span class="sxs-lookup"><span data-stu-id="ae166-103">Working with entity states</span></span>
<span data-ttu-id="ae166-104">本主題將討論如何在內容中新增和附加實體，以及在 SaveChanges 期間 Entity Framework 如何處理這些專案。</span><span class="sxs-lookup"><span data-stu-id="ae166-104">This topic will cover how to add and attach entities to a context and how Entity Framework processes these during SaveChanges.</span></span>
<span data-ttu-id="ae166-105">Entity Framework 會負責追蹤實體連接到內容時的狀態，但在中斷連線或多層式案例中，您可以讓 EF 知道您的實體應該位於何種狀態。</span><span class="sxs-lookup"><span data-stu-id="ae166-105">Entity Framework takes care of tracking the state of entities while they are connected to a context, but in disconnected or N-Tier scenarios you can let EF know what state your entities should be in.</span></span>
<span data-ttu-id="ae166-106">本主題所示範的技巧同樣適用於使用 Code First 和 EF 設計工具所建立的模型。</span><span class="sxs-lookup"><span data-stu-id="ae166-106">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

## <a name="entity-states-and-savechanges"></a><span data-ttu-id="ae166-107">實體狀態和 SaveChanges</span><span class="sxs-lookup"><span data-stu-id="ae166-107">Entity states and SaveChanges</span></span>

<span data-ttu-id="ae166-108">實體可以是 EntityState 列舉所定義的五種狀態之一。</span><span class="sxs-lookup"><span data-stu-id="ae166-108">An entity can be in one of five states as defined by the EntityState enumeration.</span></span> <span data-ttu-id="ae166-109">這些狀態包括：</span><span class="sxs-lookup"><span data-stu-id="ae166-109">These states are:</span></span>  

- <span data-ttu-id="ae166-110">已新增：實體正在由內容追蹤，但尚未存在於資料庫中</span><span class="sxs-lookup"><span data-stu-id="ae166-110">Added: the entity is being tracked by the context but does not yet exist in the database</span></span>  
- <span data-ttu-id="ae166-111">未變更：實體正在由內容追蹤，而且存在於資料庫中，且其屬性值未與資料庫中的值變更</span><span class="sxs-lookup"><span data-stu-id="ae166-111">Unchanged: the entity is being tracked by the context and exists in the database, and its property values have not changed from the values in the database</span></span>  
- <span data-ttu-id="ae166-112">已修改：實體正在由內容追蹤，而且存在於資料庫中，且部分或全部的屬性值已修改</span><span class="sxs-lookup"><span data-stu-id="ae166-112">Modified: the entity is being tracked by the context and exists in the database, and some or all of its property values have been modified</span></span>  
- <span data-ttu-id="ae166-113">已刪除：實體正在由內容追蹤，而且存在於資料庫中，但在下一次呼叫 SaveChanges 時已標示為從資料庫刪除</span><span class="sxs-lookup"><span data-stu-id="ae166-113">Deleted: the entity is being tracked by the context and exists in the database, but has been marked for deletion from the database the next time SaveChanges is called</span></span>  
- <span data-ttu-id="ae166-114">已卸離：實體未被內容追蹤</span><span class="sxs-lookup"><span data-stu-id="ae166-114">Detached: the entity is not being tracked by the context</span></span>  

<span data-ttu-id="ae166-115">SaveChanges 會針對不同狀態中的實體執行不同的專案：</span><span class="sxs-lookup"><span data-stu-id="ae166-115">SaveChanges does different things for entities in different states:</span></span>  

- <span data-ttu-id="ae166-116">SaveChanges 不會觸及未變更的實體。</span><span class="sxs-lookup"><span data-stu-id="ae166-116">Unchanged entities are not touched by SaveChanges.</span></span> <span data-ttu-id="ae166-117">針對處於未變更狀態的實體，不會將更新傳送至資料庫。</span><span class="sxs-lookup"><span data-stu-id="ae166-117">Updates are not sent to the database for entities in the Unchanged state.</span></span>  
- <span data-ttu-id="ae166-118">新增的實體會插入資料庫，然後在 SaveChanges 傳回時變成未變更。</span><span class="sxs-lookup"><span data-stu-id="ae166-118">Added entities are inserted into the database and then become Unchanged when SaveChanges returns.</span></span>  
- <span data-ttu-id="ae166-119">修改過的實體會在資料庫中更新，然後在 SaveChanges 傳回時變成未變更。</span><span class="sxs-lookup"><span data-stu-id="ae166-119">Modified entities are updated in the database and then become Unchanged when SaveChanges returns.</span></span>  
- <span data-ttu-id="ae166-120">刪除的實體會從資料庫中刪除，然後從內容卸離。</span><span class="sxs-lookup"><span data-stu-id="ae166-120">Deleted entities are deleted from the database and are then detached from the context.</span></span>  

<span data-ttu-id="ae166-121">下列範例顯示實體或實體圖形的狀態可以變更的方式。</span><span class="sxs-lookup"><span data-stu-id="ae166-121">The following examples show ways in which the state of an entity or an entity graph can be changed.</span></span>  

## <a name="adding-a-new-entity-to-the-context"></a><span data-ttu-id="ae166-122">將新實體新增至內容</span><span class="sxs-lookup"><span data-stu-id="ae166-122">Adding a new entity to the context</span></span>  

<span data-ttu-id="ae166-123">您可以藉由呼叫 DbSet 上的 Add 方法，將新的實體新增至內容。</span><span class="sxs-lookup"><span data-stu-id="ae166-123">A new entity can be added to the context by calling the Add method on DbSet.</span></span>
<span data-ttu-id="ae166-124">這會讓實體進入加入的狀態，這表示它會在下次呼叫 SaveChanges 時插入資料庫。</span><span class="sxs-lookup"><span data-stu-id="ae166-124">This puts the entity into the Added state, meaning that it will be inserted into the database the next time that SaveChanges is called.</span></span>
<span data-ttu-id="ae166-125">例如：</span><span class="sxs-lookup"><span data-stu-id="ae166-125">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = new Blog { Name = "ADO.NET Blog" };
    context.Blogs.Add(blog);
    context.SaveChanges();
}
```  

<span data-ttu-id="ae166-126">將新實體新增至內容的另一種方式是將其狀態變更為 [已新增]。</span><span class="sxs-lookup"><span data-stu-id="ae166-126">Another way to add a new entity to the context is to change its state to Added.</span></span> <span data-ttu-id="ae166-127">例如：</span><span class="sxs-lookup"><span data-stu-id="ae166-127">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = new Blog { Name = "ADO.NET Blog" };
    context.Entry(blog).State = EntityState.Added;
    context.SaveChanges();
}
```  

<span data-ttu-id="ae166-128">最後，您可以將新的實體連結到已追蹤的另一個實體，藉此將它加入至內容。</span><span class="sxs-lookup"><span data-stu-id="ae166-128">Finally, you can add a new entity to the context by hooking it up to another entity that is already being tracked.</span></span>
<span data-ttu-id="ae166-129">這可能是因為將新實體新增至另一個實體的集合導覽屬性，或將另一個實體的參考導覽屬性設定為指向新的實體。</span><span class="sxs-lookup"><span data-stu-id="ae166-129">This could be by adding the new entity to the collection navigation property of another entity or by setting a reference navigation property of another entity to point to the new entity.</span></span> <span data-ttu-id="ae166-130">例如：</span><span class="sxs-lookup"><span data-stu-id="ae166-130">For example:</span></span>  

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

<span data-ttu-id="ae166-131">請注意，在所有這些範例中，如果加入的實體具有尚未追蹤之其他實體的參考，則這些新實體也會加入至內容，並在下次呼叫 SaveChanges 時插入資料庫。</span><span class="sxs-lookup"><span data-stu-id="ae166-131">Note that for all of these examples if the entity being added has references to other entities that are not yet tracked then these new entities will also be added to the context and will be inserted into the database the next time that SaveChanges is called.</span></span>  

## <a name="attaching-an-existing-entity-to-the-context"></a><span data-ttu-id="ae166-132">將現有實體附加至內容</span><span class="sxs-lookup"><span data-stu-id="ae166-132">Attaching an existing entity to the context</span></span>  

<span data-ttu-id="ae166-133">如果您的實體已經存在於資料庫中，但目前未由內容追蹤，則您可以使用 DbSet 上的 Attach 方法告訴內容追蹤實體。</span><span class="sxs-lookup"><span data-stu-id="ae166-133">If you have an entity that you know already exists in the database but which is not currently being tracked by the context then you can tell the context to track the entity using the Attach method on DbSet.</span></span> <span data-ttu-id="ae166-134">實體在內容中會處於未變更狀態。</span><span class="sxs-lookup"><span data-stu-id="ae166-134">The entity will be in the Unchanged state in the context.</span></span> <span data-ttu-id="ae166-135">例如：</span><span class="sxs-lookup"><span data-stu-id="ae166-135">For example:</span></span>  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Blogs.Attach(existingBlog);

    // Do some more work...  

    context.SaveChanges();
}
```  

<span data-ttu-id="ae166-136">請注意，如果呼叫 SaveChanges 時未進行任何其他的附加實體操作，就不會對資料庫進行任何變更。</span><span class="sxs-lookup"><span data-stu-id="ae166-136">Note that no changes will be made to the database if SaveChanges is called without doing any other manipulation of the attached entity.</span></span> <span data-ttu-id="ae166-137">這是因為實體處於未變更狀態。</span><span class="sxs-lookup"><span data-stu-id="ae166-137">This is because the entity is in the Unchanged state.</span></span>  

<span data-ttu-id="ae166-138">將現有實體附加至內容的另一種方式是將其狀態變更為未變更。</span><span class="sxs-lookup"><span data-stu-id="ae166-138">Another way to attach an existing entity to the context is to change its state to Unchanged.</span></span> <span data-ttu-id="ae166-139">例如：</span><span class="sxs-lookup"><span data-stu-id="ae166-139">For example:</span></span>  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Entry(existingBlog).State = EntityState.Unchanged;

    // Do some more work...  

    context.SaveChanges();
}
```  

<span data-ttu-id="ae166-140">請注意，在這兩個範例中，如果附加的實體參考尚未追蹤的其他實體，這些新實體也會附加至未變更狀態的內容。</span><span class="sxs-lookup"><span data-stu-id="ae166-140">Note that for both of these examples if the entity being attached has references to other entities that are not yet tracked then these new entities will also attached to the context in the Unchanged state.</span></span>  

## <a name="attaching-an-existing-but-modified-entity-to-the-context"></a><span data-ttu-id="ae166-141">將現有但修改過的實體附加至內容</span><span class="sxs-lookup"><span data-stu-id="ae166-141">Attaching an existing but modified entity to the context</span></span>  

<span data-ttu-id="ae166-142">如果您的實體已經存在於資料庫中，但變更可能已存在，您可以告知內容附加實體，並將其狀態設定為 [已修改]。</span><span class="sxs-lookup"><span data-stu-id="ae166-142">If you have an entity that you know already exists in the database but to which changes may have been made then you can tell the context to attach the entity and set its state to Modified.</span></span>
<span data-ttu-id="ae166-143">例如：</span><span class="sxs-lookup"><span data-stu-id="ae166-143">For example:</span></span>  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Entry(existingBlog).State = EntityState.Modified;

    // Do some more work...  

    context.SaveChanges();
}
```  

<span data-ttu-id="ae166-144">當您將狀態變更為 [已修改] 時，會將實體的所有屬性標示為已修改，而且在呼叫 SaveChanges 時，所有的屬性值都會傳送至資料庫。</span><span class="sxs-lookup"><span data-stu-id="ae166-144">When you change the state to Modified all the properties of the entity will be marked as modified and all the property values will be sent to the database when SaveChanges is called.</span></span>  

<span data-ttu-id="ae166-145">請注意，如果附加的實體有參考其他尚未追蹤的實體，則這些新實體會附加至未變更狀態的內容，不會自動加以修改。</span><span class="sxs-lookup"><span data-stu-id="ae166-145">Note that if the entity being attached has references to other entities that are not yet tracked, then these new entities will attached to the context in the Unchanged state—they will not automatically be made Modified.</span></span>
<span data-ttu-id="ae166-146">如果您有多個需要標示為已修改的實體，則應該個別設定每個實體的狀態。</span><span class="sxs-lookup"><span data-stu-id="ae166-146">If you have multiple entities that need to be marked Modified you should set the state for each of these entities individually.</span></span>  

## <a name="changing-the-state-of-a-tracked-entity"></a><span data-ttu-id="ae166-147">變更追蹤實體的狀態</span><span class="sxs-lookup"><span data-stu-id="ae166-147">Changing the state of a tracked entity</span></span>  

<span data-ttu-id="ae166-148">您可以藉由在專案上設定 State 屬性，來變更已追蹤之實體的狀態。</span><span class="sxs-lookup"><span data-stu-id="ae166-148">You can change the state of an entity that is already being tracked by setting the State property on its entry.</span></span> <span data-ttu-id="ae166-149">例如：</span><span class="sxs-lookup"><span data-stu-id="ae166-149">For example:</span></span>  

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

<span data-ttu-id="ae166-150">請注意，針對已追蹤的實體呼叫 [加入] 或 [附加] 也可以用來變更實體狀態。</span><span class="sxs-lookup"><span data-stu-id="ae166-150">Note that calling Add or Attach for an entity that is already tracked can also be used to change the entity state.</span></span> <span data-ttu-id="ae166-151">例如，針對目前處於已加入狀態的實體呼叫附加，會將其狀態變更為未變更。</span><span class="sxs-lookup"><span data-stu-id="ae166-151">For example, calling Attach for an entity that is currently in the Added state will change its state to Unchanged.</span></span>  

## <a name="insert-or-update-pattern"></a><span data-ttu-id="ae166-152">Insert 或 update 模式</span><span class="sxs-lookup"><span data-stu-id="ae166-152">Insert or update pattern</span></span>  

<span data-ttu-id="ae166-153">某些應用程式的常見模式是將實體新增為新的 (導致資料庫插入) 或將實體附加為現有的，並將它標示為已修改 (產生資料庫更新) 取決於主鍵的值。</span><span class="sxs-lookup"><span data-stu-id="ae166-153">A common pattern for some applications is to either Add an entity as new (resulting in a database insert) or Attach an entity as existing and mark it as modified (resulting in a database update) depending on the value of the primary key.</span></span>
<span data-ttu-id="ae166-154">例如，使用資料庫產生的整數主鍵時，通常會將具有零索引鍵的實體視為新的，並將具有非零索引鍵的實體視為現有的實體。</span><span class="sxs-lookup"><span data-stu-id="ae166-154">For example, when using database generated integer primary keys it is common to treat an entity with a zero key as new and an entity with a non-zero key as existing.</span></span>
<span data-ttu-id="ae166-155">您可以根據主鍵值的檢查來設定實體狀態，以達成這個模式。</span><span class="sxs-lookup"><span data-stu-id="ae166-155">This pattern can be achieved by setting the entity state based on a check of the primary key value.</span></span> <span data-ttu-id="ae166-156">例如：</span><span class="sxs-lookup"><span data-stu-id="ae166-156">For example:</span></span>  

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

<span data-ttu-id="ae166-157">請注意，當您將狀態變更為 [已修改] 時，會將實體的所有屬性標示為已修改，而且在呼叫 SaveChanges 時，所有的屬性值都會傳送至資料庫。</span><span class="sxs-lookup"><span data-stu-id="ae166-157">Note that when you change the state to Modified all the properties of the entity will be marked as modified and all the property values will be sent to the database when SaveChanges is called.</span></span>  
