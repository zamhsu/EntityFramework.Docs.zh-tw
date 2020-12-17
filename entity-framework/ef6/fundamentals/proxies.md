---
title: 使用 proxy-EF6
description: 使用 Entity Framework 6 中的 proxy
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/proxies
ms.openlocfilehash: de44e891c074a8811e466d040f6451bea0f950f4
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635844"
---
# <a name="working-with-proxies"></a><span data-ttu-id="2fd18-103">使用 proxy</span><span class="sxs-lookup"><span data-stu-id="2fd18-103">Working with proxies</span></span>
<span data-ttu-id="2fd18-104">建立 POCO 實體類型的實例時，Entity Framework 通常會建立動態產生之衍生型別的實例，作為實體的 proxy。</span><span class="sxs-lookup"><span data-stu-id="2fd18-104">When creating instances of POCO entity types, Entity Framework often creates instances of a dynamically generated derived type that acts as a proxy for the entity.</span></span> <span data-ttu-id="2fd18-105">此 proxy 會覆寫實體的一些虛擬屬性，以便在存取屬性時自動插入用來執行動作的勾點。</span><span class="sxs-lookup"><span data-stu-id="2fd18-105">This proxy overrides some virtual properties of the entity to insert hooks for performing actions automatically when the property is accessed.</span></span> <span data-ttu-id="2fd18-106">例如，這項機制可用來支援關聯性的延遲載入。</span><span class="sxs-lookup"><span data-stu-id="2fd18-106">For example, this mechanism is used to support lazy loading of relationships.</span></span> <span data-ttu-id="2fd18-107">本主題所示範的技巧同樣適用於使用 Code First 和 EF 設計工具所建立的模型。</span><span class="sxs-lookup"><span data-stu-id="2fd18-107">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

## <a name="disabling-proxy-creation"></a><span data-ttu-id="2fd18-108">停用 proxy 建立</span><span class="sxs-lookup"><span data-stu-id="2fd18-108">Disabling proxy creation</span></span>  

<span data-ttu-id="2fd18-109">有時候，防止 Entity Framework 建立 proxy 實例會很有用。</span><span class="sxs-lookup"><span data-stu-id="2fd18-109">Sometimes it is useful to prevent Entity Framework from creating proxy instances.</span></span> <span data-ttu-id="2fd18-110">例如，序列化非 proxy 實例比序列化 proxy 實例簡單得多。</span><span class="sxs-lookup"><span data-stu-id="2fd18-110">For example, serializing non-proxy instances is considerably easier than serializing proxy instances.</span></span> <span data-ttu-id="2fd18-111">您可以藉由清除旗標來關閉 Proxy 建立 `ProxyCreationEnabled` 。</span><span class="sxs-lookup"><span data-stu-id="2fd18-111">Proxy creation can be turned off by clearing the `ProxyCreationEnabled` flag.</span></span> <span data-ttu-id="2fd18-112">您可以在內容的函式中，進行這項作業。</span><span class="sxs-lookup"><span data-stu-id="2fd18-112">One place you could do this is in the constructor of your context.</span></span> <span data-ttu-id="2fd18-113">例如：</span><span class="sxs-lookup"><span data-stu-id="2fd18-113">For example:</span></span>  

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext()
    {
        this.Configuration.ProxyCreationEnabled = false;
    }  

    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
}
```  

<span data-ttu-id="2fd18-114">請注意，EF 不會針對沒有 proxy 要執行的型別建立 proxy。</span><span class="sxs-lookup"><span data-stu-id="2fd18-114">Note that the EF will not create proxies for types where there is nothing for the proxy to do.</span></span> <span data-ttu-id="2fd18-115">這表示您也可以將密封的類型和/或沒有虛擬屬性，以避免 proxy。</span><span class="sxs-lookup"><span data-stu-id="2fd18-115">This means that you can also avoid proxies by having types that are sealed and/or have no virtual properties.</span></span>  

## <a name="explicitly-creating-an-instance-of-a-proxy"></a><span data-ttu-id="2fd18-116">明確建立 proxy 的實例</span><span class="sxs-lookup"><span data-stu-id="2fd18-116">Explicitly creating an instance of a proxy</span></span>  

<span data-ttu-id="2fd18-117">如果您使用 new 運算子建立實體的實例，將不會建立 proxy 實例。</span><span class="sxs-lookup"><span data-stu-id="2fd18-117">A proxy instance will not be created if you create an instance of an entity using the new operator.</span></span> <span data-ttu-id="2fd18-118">這可能不是問題，但如果您需要建立 proxy 實例 (例如，讓消極式載入或 proxy 變更追蹤正常運作) 那麼您可以使用的方法來執行這項作業 `Create` `DbSet` 。</span><span class="sxs-lookup"><span data-stu-id="2fd18-118">This may not be a problem, but if you need to create a proxy instance (for example, so that lazy loading or proxy change tracking will work) then you can do so using the `Create` method of `DbSet`.</span></span> <span data-ttu-id="2fd18-119">例如：</span><span class="sxs-lookup"><span data-stu-id="2fd18-119">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Create();
}
```  

<span data-ttu-id="2fd18-120">`Create`如果您想要建立衍生實體類型的實例，則可以使用的泛型版本。</span><span class="sxs-lookup"><span data-stu-id="2fd18-120">The generic version of `Create` can be used if you want to create an instance of a derived entity type.</span></span> <span data-ttu-id="2fd18-121">例如：</span><span class="sxs-lookup"><span data-stu-id="2fd18-121">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var admin = context.Users.Create<Administrator>();
}
```  

<span data-ttu-id="2fd18-122">請注意，此 `Create` 方法不會將建立的實體新增或附加至內容。</span><span class="sxs-lookup"><span data-stu-id="2fd18-122">Note that the `Create` method does not add or attach the created entity to the context.</span></span>  

<span data-ttu-id="2fd18-123">請注意， `Create` 如果為實體建立 proxy 型別不會有任何值，此方法只會建立實體型別本身的實例，因為它不會進行任何動作。</span><span class="sxs-lookup"><span data-stu-id="2fd18-123">Note that the `Create` method will just create an instance of the entity type itself if creating a proxy type for the entity would have no value because it would not do anything.</span></span> <span data-ttu-id="2fd18-124">例如，如果實體類型是密封的，且（或）沒有虛擬屬性，則 `Create` 只會建立實體類型的實例。</span><span class="sxs-lookup"><span data-stu-id="2fd18-124">For example, if the entity type is sealed and/or has no virtual properties then `Create` will just create an instance of the entity type.</span></span>  

## <a name="getting-the-actual-entity-type-from-a-proxy-type"></a><span data-ttu-id="2fd18-125">從 proxy 類型取得實際的實體類型</span><span class="sxs-lookup"><span data-stu-id="2fd18-125">Getting the actual entity type from a proxy type</span></span>  

<span data-ttu-id="2fd18-126">Proxy 類型的名稱看起來像這樣：</span><span class="sxs-lookup"><span data-stu-id="2fd18-126">Proxy types have names that look something like this:</span></span>  

```
System.Data.Entity.DynamicProxies.Blog_5E43C6C196972BF0754973E48C9C941092D86818CD94005E9A759B70BF6E48E6
```

<span data-ttu-id="2fd18-127">您可以使用中的方法，尋找此 proxy 類型的實體類型 `GetObjectType` `ObjectContext` 。</span><span class="sxs-lookup"><span data-stu-id="2fd18-127">You can find the entity type for this proxy type using the `GetObjectType` method from `ObjectContext`.</span></span> <span data-ttu-id="2fd18-128">例如：</span><span class="sxs-lookup"><span data-stu-id="2fd18-128">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    var entityType = ObjectContext.GetObjectType(blog.GetType());
}
```  

<span data-ttu-id="2fd18-129">請注意，如果傳遞給的型別 `GetObjectType` 是不是 proxy 型別的實體型別實例，則仍然會傳回實體的型別。</span><span class="sxs-lookup"><span data-stu-id="2fd18-129">Note that if the type passed to `GetObjectType` is an instance of an entity type that is not a proxy type then the type of entity is still returned.</span></span> <span data-ttu-id="2fd18-130">這表示您一律可以使用這個方法來取得實際的實體型別，而不需要任何其他檢查，就能查看型別是否為 proxy 型別。</span><span class="sxs-lookup"><span data-stu-id="2fd18-130">This means you can always use this method to get the actual entity type without any other checking to see if the type is a proxy type or not.</span></span>  
