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
# <a name="working-with-entity-states"></a><span data-ttu-id="2a930-102">使用實體狀態</span><span class="sxs-lookup"><span data-stu-id="2a930-102">Working with entity states</span></span>
<span data-ttu-id="2a930-103">本主題將說明如何將實體加入和附加至內容，以及 Entity Framework 如何在 SaveChanges 期間處理這些專案。</span><span class="sxs-lookup"><span data-stu-id="2a930-103">This topic will cover how to add and attach entities to a context and how Entity Framework processes these during SaveChanges.</span></span>
<span data-ttu-id="2a930-104">Entity Framework 會負責追蹤實體連接到內容時的狀態，但是在中斷連線或多層式案例中，您可以讓 EF 知道您的實體應該位於哪一個狀態。</span><span class="sxs-lookup"><span data-stu-id="2a930-104">Entity Framework takes care of tracking the state of entities while they are connected to a context, but in disconnected or N-Tier scenarios you can let EF know what state your entities should be in.</span></span>
<span data-ttu-id="2a930-105">本主題所示範的技巧同樣適用於使用 Code First 和 EF 設計工具所建立的模型。</span><span class="sxs-lookup"><span data-stu-id="2a930-105">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

## <a name="entity-states-and-savechanges"></a><span data-ttu-id="2a930-106">實體狀態和 SaveChanges</span><span class="sxs-lookup"><span data-stu-id="2a930-106">Entity states and SaveChanges</span></span>

<span data-ttu-id="2a930-107">實體可以是 EntityState 列舉所定義的五種狀態之一。</span><span class="sxs-lookup"><span data-stu-id="2a930-107">An entity can be in one of five states as defined by the EntityState enumeration.</span></span> <span data-ttu-id="2a930-108">這些狀態包括：</span><span class="sxs-lookup"><span data-stu-id="2a930-108">These states are:</span></span>  

- <span data-ttu-id="2a930-109">已加入：實體正在由內容追蹤，但尚未存在於資料庫中</span><span class="sxs-lookup"><span data-stu-id="2a930-109">Added: the entity is being tracked by the context but does not yet exist in the database</span></span>  
- <span data-ttu-id="2a930-110">未變更：實體正在由內容追蹤，而且存在於資料庫中，而且其屬性值尚未從資料庫中的值變更</span><span class="sxs-lookup"><span data-stu-id="2a930-110">Unchanged: the entity is being tracked by the context and exists in the database, and its property values have not changed from the values in the database</span></span>  
- <span data-ttu-id="2a930-111">已修改：實體正在由內容追蹤，而且存在於資料庫中，而且部分或全部的屬性值已修改</span><span class="sxs-lookup"><span data-stu-id="2a930-111">Modified: the entity is being tracked by the context and exists in the database, and some or all of its property values have been modified</span></span>  
- <span data-ttu-id="2a930-112">已刪除：實體正在由內容追蹤，而且存在於資料庫中，但在下一次呼叫 SaveChanges 時，已標記為要從資料庫中刪除</span><span class="sxs-lookup"><span data-stu-id="2a930-112">Deleted: the entity is being tracked by the context and exists in the database, but has been marked for deletion from the database the next time SaveChanges is called</span></span>  
- <span data-ttu-id="2a930-113">已卸離：實體未由內容追蹤</span><span class="sxs-lookup"><span data-stu-id="2a930-113">Detached: the entity is not being tracked by the context</span></span>  

<span data-ttu-id="2a930-114">SaveChanges 會針對不同狀態的實體執行不同的動作：</span><span class="sxs-lookup"><span data-stu-id="2a930-114">SaveChanges does different things for entities in different states:</span></span>  

- <span data-ttu-id="2a930-115">SaveChanges 不觸及未變更的實體。</span><span class="sxs-lookup"><span data-stu-id="2a930-115">Unchanged entities are not touched by SaveChanges.</span></span> <span data-ttu-id="2a930-116">對於處於未變更狀態的實體，不會將更新傳送至資料庫。</span><span class="sxs-lookup"><span data-stu-id="2a930-116">Updates are not sent to the database for entities in the Unchanged state.</span></span>  
- <span data-ttu-id="2a930-117">新增的實體會插入至資料庫，然後在 SaveChanges 傳回時變得不變。</span><span class="sxs-lookup"><span data-stu-id="2a930-117">Added entities are inserted into the database and then become Unchanged when SaveChanges returns.</span></span>  
- <span data-ttu-id="2a930-118">修改過的實體會在資料庫中更新，然後在 SaveChanges 傳回時變得不變。</span><span class="sxs-lookup"><span data-stu-id="2a930-118">Modified entities are updated in the database and then become Unchanged when SaveChanges returns.</span></span>  
- <span data-ttu-id="2a930-119">已刪除的實體會從資料庫中刪除，然後從內容卸離。</span><span class="sxs-lookup"><span data-stu-id="2a930-119">Deleted entities are deleted from the database and are then detached from the context.</span></span>  

<span data-ttu-id="2a930-120">下列範例會顯示實體或實體圖形的狀態可以變更的方式。</span><span class="sxs-lookup"><span data-stu-id="2a930-120">The following examples show ways in which the state of an entity or an entity graph can be changed.</span></span>  

## <a name="adding-a-new-entity-to-the-context"></a><span data-ttu-id="2a930-121">將新實體新增至內容</span><span class="sxs-lookup"><span data-stu-id="2a930-121">Adding a new entity to the context</span></span>  

<span data-ttu-id="2a930-122">可以藉由在 DbSet 上呼叫 Add 方法，將新的實體加入至內容中。</span><span class="sxs-lookup"><span data-stu-id="2a930-122">A new entity can be added to the context by calling the Add method on DbSet.</span></span>
<span data-ttu-id="2a930-123">這會讓實體進入已加入的狀態，這表示它會在下次呼叫 SaveChanges 時插入至資料庫。</span><span class="sxs-lookup"><span data-stu-id="2a930-123">This puts the entity into the Added state, meaning that it will be inserted into the database the next time that SaveChanges is called.</span></span>
<span data-ttu-id="2a930-124">例如：</span><span class="sxs-lookup"><span data-stu-id="2a930-124">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = new Blog { Name = "ADO.NET Blog" };
    context.Blogs.Add(blog);
    context.SaveChanges();
}
```  

<span data-ttu-id="2a930-125">另一個將新實體新增至內容的方法，是將其狀態變更為 [已新增]。</span><span class="sxs-lookup"><span data-stu-id="2a930-125">Another way to add a new entity to the context is to change its state to Added.</span></span> <span data-ttu-id="2a930-126">例如：</span><span class="sxs-lookup"><span data-stu-id="2a930-126">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = new Blog { Name = "ADO.NET Blog" };
    context.Entry(blog).State = EntityState.Added;
    context.SaveChanges();
}
```  

<span data-ttu-id="2a930-127">最後，您可以將新實體連結至內容，方法是將它連結到已追蹤的另一個實體。</span><span class="sxs-lookup"><span data-stu-id="2a930-127">Finally, you can add a new entity to the context by hooking it up to another entity that is already being tracked.</span></span>
<span data-ttu-id="2a930-128">這可能是藉由將新實體新增至另一個實體的集合導覽屬性，或設定另一個實體的參考導覽屬性來指向新的實體。</span><span class="sxs-lookup"><span data-stu-id="2a930-128">This could be by adding the new entity to the collection navigation property of another entity or by setting a reference navigation property of another entity to point to the new entity.</span></span> <span data-ttu-id="2a930-129">例如：</span><span class="sxs-lookup"><span data-stu-id="2a930-129">For example:</span></span>  

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

<span data-ttu-id="2a930-130">請注意，在這些範例中，如果要加入的實體具有尚未追蹤之其他實體的參考，則這些新的實體也會加入至內容中，並在下次呼叫 SaveChanges 時插入資料庫。</span><span class="sxs-lookup"><span data-stu-id="2a930-130">Note that for all of these examples if the entity being added has references to other entities that are not yet tracked then these new entities will also be added to the context and will be inserted into the database the next time that SaveChanges is called.</span></span>  

## <a name="attaching-an-existing-entity-to-the-context"></a><span data-ttu-id="2a930-131">將現有的實體附加至內容</span><span class="sxs-lookup"><span data-stu-id="2a930-131">Attaching an existing entity to the context</span></span>  

<span data-ttu-id="2a930-132">如果您的實體已經存在於資料庫中，但目前未由內容追蹤，您可以使用 DbSet 上的 Attach 方法，告訴內容追蹤該實體。</span><span class="sxs-lookup"><span data-stu-id="2a930-132">If you have an entity that you know already exists in the database but which is not currently being tracked by the context then you can tell the context to track the entity using the Attach method on DbSet.</span></span> <span data-ttu-id="2a930-133">實體在內容中的狀態會是 [未變更]。</span><span class="sxs-lookup"><span data-stu-id="2a930-133">The entity will be in the Unchanged state in the context.</span></span> <span data-ttu-id="2a930-134">例如：</span><span class="sxs-lookup"><span data-stu-id="2a930-134">For example:</span></span>  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Blogs.Attach(existingBlog);

    // Do some more work...  

    context.SaveChanges();
}
```  

<span data-ttu-id="2a930-135">請注意，如果呼叫 SaveChanges，而不進行任何其他的附加實體操作，則不會對資料庫進行任何變更。</span><span class="sxs-lookup"><span data-stu-id="2a930-135">Note that no changes will be made to the database if SaveChanges is called without doing any other manipulation of the attached entity.</span></span> <span data-ttu-id="2a930-136">這是因為實體處於未變更狀態。</span><span class="sxs-lookup"><span data-stu-id="2a930-136">This is because the entity is in the Unchanged state.</span></span>  

<span data-ttu-id="2a930-137">另一個將現有實體附加至內容的方法，是將其狀態變更為 [未變更]。</span><span class="sxs-lookup"><span data-stu-id="2a930-137">Another way to attach an existing entity to the context is to change its state to Unchanged.</span></span> <span data-ttu-id="2a930-138">例如：</span><span class="sxs-lookup"><span data-stu-id="2a930-138">For example:</span></span>  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Entry(existingBlog).State = EntityState.Unchanged;

    // Do some more work...  

    context.SaveChanges();
}
```  

<span data-ttu-id="2a930-139">請注意，在這兩個範例中，如果附加的實體具有尚未追蹤之其他實體的參考，則這些新的實體也會附加至未變更狀態的內容。</span><span class="sxs-lookup"><span data-stu-id="2a930-139">Note that for both of these examples if the entity being attached has references to other entities that are not yet tracked then these new entities will also attached to the context in the Unchanged state.</span></span>  

## <a name="attaching-an-existing-but-modified-entity-to-the-context"></a><span data-ttu-id="2a930-140">將現有但已修改的實體附加至內容</span><span class="sxs-lookup"><span data-stu-id="2a930-140">Attaching an existing but modified entity to the context</span></span>  

<span data-ttu-id="2a930-141">如果您的實體已經存在於資料庫中，但可能已進行變更，則可以告訴內容附加實體，並將其狀態設定為 [已修改]。</span><span class="sxs-lookup"><span data-stu-id="2a930-141">If you have an entity that you know already exists in the database but to which changes may have been made then you can tell the context to attach the entity and set its state to Modified.</span></span>
<span data-ttu-id="2a930-142">例如：</span><span class="sxs-lookup"><span data-stu-id="2a930-142">For example:</span></span>  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Entry(existingBlog).State = EntityState.Modified;

    // Do some more work...  

    context.SaveChanges();
}
```  

<span data-ttu-id="2a930-143">當您變更狀態以修改時，實體的所有屬性都會標示為已修改，而且在呼叫 SaveChanges 時，所有屬性值都會傳送至資料庫。</span><span class="sxs-lookup"><span data-stu-id="2a930-143">When you change the state to Modified all the properties of the entity will be marked as modified and all the property values will be sent to the database when SaveChanges is called.</span></span>  

<span data-ttu-id="2a930-144">請注意，如果所附加的實體具有其他尚未追蹤之實體的參考，則這些新的實體會附加至未變更狀態的內容，而不會自動進行修改。</span><span class="sxs-lookup"><span data-stu-id="2a930-144">Note that if the entity being attached has references to other entities that are not yet tracked, then these new entities will attached to the context in the Unchanged state—they will not automatically be made Modified.</span></span>
<span data-ttu-id="2a930-145">如果您有多個需要標示為已修改的實體，您應該個別設定這些實體的狀態。</span><span class="sxs-lookup"><span data-stu-id="2a930-145">If you have multiple entities that need to be marked Modified you should set the state for each of these entities individually.</span></span>  

## <a name="changing-the-state-of-a-tracked-entity"></a><span data-ttu-id="2a930-146">變更已追蹤實體的狀態</span><span class="sxs-lookup"><span data-stu-id="2a930-146">Changing the state of a tracked entity</span></span>  

<span data-ttu-id="2a930-147">您可以藉由在其專案上設定 State 屬性，來變更已追蹤之實體的狀態。</span><span class="sxs-lookup"><span data-stu-id="2a930-147">You can change the state of an entity that is already being tracked by setting the State property on its entry.</span></span> <span data-ttu-id="2a930-148">例如：</span><span class="sxs-lookup"><span data-stu-id="2a930-148">For example:</span></span>  

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

<span data-ttu-id="2a930-149">請注意，針對已追蹤的實體呼叫 [加入] 或 [附加] 也可以用來變更實體狀態。</span><span class="sxs-lookup"><span data-stu-id="2a930-149">Note that calling Add or Attach for an entity that is already tracked can also be used to change the entity state.</span></span> <span data-ttu-id="2a930-150">例如，針對目前處於已加入狀態的實體呼叫 Attach，會將其狀態變更為不變。</span><span class="sxs-lookup"><span data-stu-id="2a930-150">For example, calling Attach for an entity that is currently in the Added state will change its state to Unchanged.</span></span>  

## <a name="insert-or-update-pattern"></a><span data-ttu-id="2a930-151">插入或更新模式</span><span class="sxs-lookup"><span data-stu-id="2a930-151">Insert or update pattern</span></span>  

<span data-ttu-id="2a930-152">某些應用程式的常見模式是將實體新增為新的（產生資料庫插入），或將實體附加為現有的，並將它標示為已修改（產生資料庫更新），視主鍵的值而定。</span><span class="sxs-lookup"><span data-stu-id="2a930-152">A common pattern for some applications is to either Add an entity as new (resulting in a database insert) or Attach an entity as existing and mark it as modified (resulting in a database update) depending on the value of the primary key.</span></span>
<span data-ttu-id="2a930-153">例如，當使用資料庫產生的整數主鍵時，通常會將具有零索引鍵的實體視為新，並將具有非零索引鍵的實體當做現有的。</span><span class="sxs-lookup"><span data-stu-id="2a930-153">For example, when using database generated integer primary keys it is common to treat an entity with a zero key as new and an entity with a non-zero key as existing.</span></span>
<span data-ttu-id="2a930-154">您可以根據主鍵值的檢查設定實體狀態來達成此模式。</span><span class="sxs-lookup"><span data-stu-id="2a930-154">This pattern can be achieved by setting the entity state based on a check of the primary key value.</span></span> <span data-ttu-id="2a930-155">例如：</span><span class="sxs-lookup"><span data-stu-id="2a930-155">For example:</span></span>  

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

<span data-ttu-id="2a930-156">請注意，當您變更狀態以修改時，實體的所有屬性都會標示為已修改，而且在呼叫 SaveChanges 時，所有屬性值都會傳送至資料庫。</span><span class="sxs-lookup"><span data-stu-id="2a930-156">Note that when you change the state to Modified all the properties of the entity will be marked as modified and all the property values will be sent to the database when SaveChanges is called.</span></span>  
