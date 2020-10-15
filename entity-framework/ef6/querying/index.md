---
title: 查詢和尋找實體 - EF6
description: 在 Entity Framework 6 中查詢及尋找實體
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/querying/index
ms.openlocfilehash: ec0239ed54099226d009031af79388f5ae00cdc4
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065887"
---
# <a name="querying-and-finding-entities"></a><span data-ttu-id="21e2d-103">查詢和尋找實體</span><span class="sxs-lookup"><span data-stu-id="21e2d-103">Querying and Finding Entities</span></span>
<span data-ttu-id="21e2d-104">本主題涵蓋您可以使用 Entity Framework 查詢資料的各種方法，包括 LINQ 和 Find 方法。</span><span class="sxs-lookup"><span data-stu-id="21e2d-104">This topic covers the various ways you can query for data using Entity Framework, including LINQ and the Find method.</span></span> <span data-ttu-id="21e2d-105">本主題所示範的技巧同樣適用於使用 Code First 和 EF 設計工具所建立的模型。</span><span class="sxs-lookup"><span data-stu-id="21e2d-105">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

## <a name="finding-entities-using-a-query"></a><span data-ttu-id="21e2d-106">使用查詢尋找實體</span><span class="sxs-lookup"><span data-stu-id="21e2d-106">Finding entities using a query</span></span>  

<span data-ttu-id="21e2d-107">DbSet 和 IDbSet 會實作 IQueryable，因此可作為針對資料庫撰寫 LINQ 查詢的起點。</span><span class="sxs-lookup"><span data-stu-id="21e2d-107">DbSet and IDbSet implement IQueryable and so can be used as the starting point for writing a LINQ query against the database.</span></span> <span data-ttu-id="21e2d-108">這並不是深入討論 LINQ　的適當位置，但以下是一些簡單範例：</span><span class="sxs-lookup"><span data-stu-id="21e2d-108">This is not the appropriate place for an in-depth discussion of LINQ, but here are a couple of simple examples:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    // Query for all blogs with names starting with B
    var blogs = from b in context.Blogs
                   where b.Name.StartsWith("B")
                   select b;

    // Query for the Blog named ADO.NET Blog
    var blog = context.Blogs
                    .Where(b => b.Name == "ADO.NET Blog")
                    .FirstOrDefault();
}
```  

<span data-ttu-id="21e2d-109">請注意，DbSet 和 IDbSet 一律會針對資料庫建立查詢，即使傳回的實體已存在於內容中，還是一律會對資料庫來回查詢。</span><span class="sxs-lookup"><span data-stu-id="21e2d-109">Note that DbSet and IDbSet always create queries against the database and will always involve a round trip to the database even if the entities returned already exist in the context.</span></span> <span data-ttu-id="21e2d-110">針對資料庫執行查詢的時機：</span><span class="sxs-lookup"><span data-stu-id="21e2d-110">A query is executed against the database when:</span></span>  

- <span data-ttu-id="21e2d-111">它是由 **foreach** (C#) 或 **For Each** (Visual Basic) 陳述式所列舉。</span><span class="sxs-lookup"><span data-stu-id="21e2d-111">It is enumerated by a **foreach** (C#) or **For Each** (Visual Basic) statement.</span></span>  
- <span data-ttu-id="21e2d-112">它是由 [ToArray](https://msdn.microsoft.com/library/bb298736)、[ToDictionary](https://msdn.microsoft.com/library/system.linq.enumerable.todictionary) 或 [ToList](https://msdn.microsoft.com/library/bb342261) 等集合作業所列舉。</span><span class="sxs-lookup"><span data-stu-id="21e2d-112">It is enumerated by a collection operation such as [ToArray](https://msdn.microsoft.com/library/bb298736), [ToDictionary](https://msdn.microsoft.com/library/system.linq.enumerable.todictionary), or [ToList](https://msdn.microsoft.com/library/bb342261).</span></span>  
- <span data-ttu-id="21e2d-113">LINQ 運算子 (例如 [First](https://msdn.microsoft.com/library/bb291976) 或 [Any](https://msdn.microsoft.com/library/bb337697)) 是在查詢的最外面部分中指定。</span><span class="sxs-lookup"><span data-stu-id="21e2d-113">LINQ operators such as [First](https://msdn.microsoft.com/library/bb291976) or [Any](https://msdn.microsoft.com/library/bb337697) are specified in the outermost part of the query.</span></span>  
- <span data-ttu-id="21e2d-114">會呼叫下列方法：DbSet 上的 [Load](https://msdn.microsoft.com/library/system.data.entity.dbextensions.load) 擴充方法、[DbEntityEntry.Reload](https://msdn.microsoft.com/library/system.data.entity.infrastructure.dbentityentry.reload.aspx) 和 Database.ExecuteSqlCommand。</span><span class="sxs-lookup"><span data-stu-id="21e2d-114">The following methods are called: the [Load](https://msdn.microsoft.com/library/system.data.entity.dbextensions.load) extension method on a DbSet, [DbEntityEntry.Reload](https://msdn.microsoft.com/library/system.data.entity.infrastructure.dbentityentry.reload.aspx), and Database.ExecuteSqlCommand.</span></span>  

<span data-ttu-id="21e2d-115">從資料庫傳回結果時，內容中不存在的物件會附加到內容。</span><span class="sxs-lookup"><span data-stu-id="21e2d-115">When results are returned from the database, objects that do not exist in the context are attached to the context.</span></span> <span data-ttu-id="21e2d-116">如果某個物件已經存在於內容中，就會傳回現有的物件 (**不會**使用資料庫值來覆寫項目中物件屬性的目前和原始值)。</span><span class="sxs-lookup"><span data-stu-id="21e2d-116">If an object is already in the context, the existing object is returned (the current and original values of the object's properties in the entry are **not** overwritten with database values).</span></span>  

<span data-ttu-id="21e2d-117">當您執行查詢時，已新增至內容但尚未儲存到資料庫的實體，並不會作為結果集的一部分傳回。</span><span class="sxs-lookup"><span data-stu-id="21e2d-117">When you perform a query, entities that have been added to the context but have not yet been saved to the database are not returned as part of the result set.</span></span> <span data-ttu-id="21e2d-118">若要取得內容中的資料，請參閱[本機資料](xref:ef6/querying/local-data)。</span><span class="sxs-lookup"><span data-stu-id="21e2d-118">To get the data that is in the context, see [Local Data](xref:ef6/querying/local-data).</span></span>  

<span data-ttu-id="21e2d-119">如果查詢未從資料庫傳回任何資料列，結果將會是空集合，而非 **null**。</span><span class="sxs-lookup"><span data-stu-id="21e2d-119">If a query returns no rows from the database, the result will be an empty collection, rather than **null**.</span></span>  

## <a name="finding-entities-using-primary-keys"></a><span data-ttu-id="21e2d-120">使用主索引鍵尋找實體</span><span class="sxs-lookup"><span data-stu-id="21e2d-120">Finding entities using primary keys</span></span>  

<span data-ttu-id="21e2d-121">DbSet 上的 Find 方法會使用主索引鍵值，嘗試尋找內容所追蹤的實體。</span><span class="sxs-lookup"><span data-stu-id="21e2d-121">The Find method on DbSet uses the primary key value to attempt to find an entity tracked by the context.</span></span> <span data-ttu-id="21e2d-122">如果內容中找不到此實體，則會將查詢傳送到資料庫，以便在該處尋找實體。</span><span class="sxs-lookup"><span data-stu-id="21e2d-122">If the entity is not found in the context then a query will be sent to the database to find the entity there.</span></span> <span data-ttu-id="21e2d-123">如果內容或資料庫中找不到此實體，就會傳回 null。</span><span class="sxs-lookup"><span data-stu-id="21e2d-123">Null is returned if the entity is not found in the context or in the database.</span></span>  

<span data-ttu-id="21e2d-124">Find 不同於以兩種顯著方式使用查詢：</span><span class="sxs-lookup"><span data-stu-id="21e2d-124">Find is different from using a query in two significant ways:</span></span>  

- <span data-ttu-id="21e2d-125">只有在內容中找不到具有指定索引鍵的實體時，才會進行資料庫的反覆存取。</span><span class="sxs-lookup"><span data-stu-id="21e2d-125">A round-trip to the database will only be made if the entity with the given key is not found in the context.</span></span>  
- <span data-ttu-id="21e2d-126">Find 將會傳回處於 [已新增] 狀態的實體。</span><span class="sxs-lookup"><span data-stu-id="21e2d-126">Find will return entities that are in the Added state.</span></span> <span data-ttu-id="21e2d-127">也就是說，Find 將傳回已新增至內容，但尚未儲存到資料庫的實體。</span><span class="sxs-lookup"><span data-stu-id="21e2d-127">That is, Find will return entities that have been added to the context but have not yet been saved to the database.</span></span>  
### <a name="finding-an-entity-by-primary-key"></a><span data-ttu-id="21e2d-128">透過主索引鍵來尋找實體</span><span class="sxs-lookup"><span data-stu-id="21e2d-128">Finding an entity by primary key</span></span>  

<span data-ttu-id="21e2d-129">下列程式碼示範 Find 的一些用法：</span><span class="sxs-lookup"><span data-stu-id="21e2d-129">The following code shows some uses of Find:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    // Will hit the database
    var blog = context.Blogs.Find(3);

    // Will return the same instance without hitting the database
    var blogAgain = context.Blogs.Find(3);

    context.Blogs.Add(new Blog { Id = -1 });

    // Will find the new blog even though it does not exist in the database
    var newBlog = context.Blogs.Find(-1);

    // Will find a User which has a string primary key
    var user = context.Users.Find("johndoe1987");
}
```  

### <a name="finding-an-entity-by-composite-primary-key"></a><span data-ttu-id="21e2d-130">透過複合主索引鍵來尋找實體</span><span class="sxs-lookup"><span data-stu-id="21e2d-130">Finding an entity by composite primary key</span></span>  

<span data-ttu-id="21e2d-131">Entity Framework 允許您的實體具有複合索引鍵 - 這是由多個屬性組成的索引鍵。</span><span class="sxs-lookup"><span data-stu-id="21e2d-131">Entity Framework allows your entities to have composite keys - that's a key that is made up of more than one property.</span></span> <span data-ttu-id="21e2d-132">例如，您可能擁有 BlogSettings 實體，其代表特定部落格的使用者設定。</span><span class="sxs-lookup"><span data-stu-id="21e2d-132">For example, you could have a BlogSettings entity that represents a users settings for a particular blog.</span></span> <span data-ttu-id="21e2d-133">由於使用者的每個部落格永遠只有一個 BlogSettings，因此您可以選擇將 BlogSettings 的主索引鍵設為 BlogId 和使用者名稱的組合。</span><span class="sxs-lookup"><span data-stu-id="21e2d-133">Because a user would only ever have one BlogSettings for each blog you could chose to make the primary key of BlogSettings a combination of BlogId and Username.</span></span> <span data-ttu-id="21e2d-134">下列程式碼會嘗試尋找 BlogId 為 3 且 Username 為 "johndoe1987" 的 BlogSettings：</span><span class="sxs-lookup"><span data-stu-id="21e2d-134">The following code attempts to find the BlogSettings with BlogId = 3 and Username = "johndoe1987":</span></span>  

``` csharp  
using (var context = new BloggingContext())
{
    var settings = context.BlogSettings.Find(3, "johndoe1987");
}
```  

<span data-ttu-id="21e2d-135">請注意，當您擁有複合索引鍵時，需要使用 ColumnAttribute 或 Fluent API 來指定複合索引鍵屬性的順序。</span><span class="sxs-lookup"><span data-stu-id="21e2d-135">Note that when you have composite keys you need to use ColumnAttribute or the fluent API to specify an ordering for the properties of the composite key.</span></span> <span data-ttu-id="21e2d-136">在指定構成索引鍵的值時，對 Find 的呼叫必須使用此順序。</span><span class="sxs-lookup"><span data-stu-id="21e2d-136">The call to Find must use this order when specifying the values that form the key.</span></span>  
