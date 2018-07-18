---
title: 使用 proxy-EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 869ee4dc-06f1-471d-8e0e-0a1a2bc59c30
caps.latest.revision: 3
ms.openlocfilehash: 4632e246d28a3cd53dabe5ac76e44f4538739abc
ms.sourcegitcommit: f05e7b62584cf228f17390bb086a61d505712e1b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2018
ms.locfileid: "39120232"
---
# <a name="working-with-proxies"></a><span data-ttu-id="f5e8f-102">使用 proxy</span><span class="sxs-lookup"><span data-stu-id="f5e8f-102">Working with proxies</span></span>
<span data-ttu-id="f5e8f-103">在建立 POCO 實體類型執行個體時，Entity Framework 通常會建立動態產生的衍生型別，做為實體的 proxy 的執行個體。</span><span class="sxs-lookup"><span data-stu-id="f5e8f-103">When creating instances of POCO entity types, Entity Framework often creates instances of a dynamically generated derived type that acts as a proxy for the entity.</span></span> <span data-ttu-id="f5e8f-104">此 proxy 會覆寫要插入存取屬性時，就會自動執行動作的攔截程序之實體的某些虛擬屬性。</span><span class="sxs-lookup"><span data-stu-id="f5e8f-104">This proxy overrides some virtual properties of the entity to insert hooks for performing actions automatically when the property is accessed.</span></span> <span data-ttu-id="f5e8f-105">比方說，這項機制用來支援關聯性的消極式載入。</span><span class="sxs-lookup"><span data-stu-id="f5e8f-105">For example, this mechanism is used to support lazy loading of relationships.</span></span> <span data-ttu-id="f5e8f-106">本主題所示範的技巧同樣適用於使用 Code First 和 EF 設計工具所建立的模型。</span><span class="sxs-lookup"><span data-stu-id="f5e8f-106">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

## <a name="disabling-proxy-creation"></a><span data-ttu-id="f5e8f-107">停用 proxy 建立</span><span class="sxs-lookup"><span data-stu-id="f5e8f-107">Disabling proxy creation</span></span>  

<span data-ttu-id="f5e8f-108">有時候它適合防止 Entity Framework 建立 proxy 執行個體。</span><span class="sxs-lookup"><span data-stu-id="f5e8f-108">Sometimes it is useful to prevent Entity Framework from creating proxy instances.</span></span> <span data-ttu-id="f5e8f-109">比方說，序列化非 proxy 執行個體可達事半功倍比序列化 proxy 執行個體。</span><span class="sxs-lookup"><span data-stu-id="f5e8f-109">For example, serializing non-proxy instances is considerably easier than serializing proxy instances.</span></span> <span data-ttu-id="f5e8f-110">Proxy 建立可以藉由清除 ProxyCreationEnabled 旗標關閉。</span><span class="sxs-lookup"><span data-stu-id="f5e8f-110">Proxy creation can be turned off by clearing the ProxyCreationEnabled flag.</span></span> <span data-ttu-id="f5e8f-111">可以這麼做的一個位置是在您內容的建構函式。</span><span class="sxs-lookup"><span data-stu-id="f5e8f-111">One place you could do this is in the constructor of your context.</span></span> <span data-ttu-id="f5e8f-112">例如: </span><span class="sxs-lookup"><span data-stu-id="f5e8f-112">For example:</span></span>  

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

<span data-ttu-id="f5e8f-113">請注意，EF 會建立型別的 proxy 其中沒有任何要執行的 proxy。</span><span class="sxs-lookup"><span data-stu-id="f5e8f-113">Note that the EF will not create proxies for types where there is nothing for the proxy to do.</span></span> <span data-ttu-id="f5e8f-114">這表示您也可以避免 proxy 擁有密封格式，和/或沒有任何虛擬屬性的型別。</span><span class="sxs-lookup"><span data-stu-id="f5e8f-114">This means that you can also avoid proxies by having types that are sealed and/or have no virtual properties.</span></span>  

## <a name="explicitly-creating-an-instance-of-a-proxy"></a><span data-ttu-id="f5e8f-115">明確建立 proxy 的執行個體</span><span class="sxs-lookup"><span data-stu-id="f5e8f-115">Explicitly creating an instance of a proxy</span></span>  

<span data-ttu-id="f5e8f-116">如果您建立使用新的運算子實體的執行個體，將不會建立 proxy 執行個體。</span><span class="sxs-lookup"><span data-stu-id="f5e8f-116">A proxy instance will not be created if you create an instance of an entity using the new operator.</span></span> <span data-ttu-id="f5e8f-117">這可能不是問題，但如果您要建立的 proxy 執行個體 （例如，讓消極式載入或 proxy 變更追蹤會運作） 然後您可以這樣使用 DbSet 的 Create 方法。</span><span class="sxs-lookup"><span data-stu-id="f5e8f-117">This may not be a problem, but if you need to create a proxy instance (for example, so that lazy loading or proxy change tracking will work) then you can do so using the Create method of DbSet.</span></span> <span data-ttu-id="f5e8f-118">例如: </span><span class="sxs-lookup"><span data-stu-id="f5e8f-118">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Create();
}
```  

<span data-ttu-id="f5e8f-119">如果您想要建立衍生的實體型別的執行個體，則可以使用建立的泛型版本。</span><span class="sxs-lookup"><span data-stu-id="f5e8f-119">The generic version of Create can be used if you want to create an instance of a derived entity type.</span></span> <span data-ttu-id="f5e8f-120">例如: </span><span class="sxs-lookup"><span data-stu-id="f5e8f-120">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var admin = context.Users.Create<Administrator>();
}
```  

<span data-ttu-id="f5e8f-121">請注意，Create 方法不會加入或建立的實體附加至內容。</span><span class="sxs-lookup"><span data-stu-id="f5e8f-121">Note that the Create method does not add or attach the created entity to the context.</span></span>  

<span data-ttu-id="f5e8f-122">建立方法會只是建立實體型別本身的執行個體如果建立 proxy 型別之實體的附註會有任何值，因為它不會執行任何動作。</span><span class="sxs-lookup"><span data-stu-id="f5e8f-122">Note that the Create method will just create an instance of the entity type itself if creating a proxy type for the entity would have no value because it would not do anything.</span></span> <span data-ttu-id="f5e8f-123">例如，如果實體類型密封格式，及/或有任何虛擬屬性，則只會建立建立實體類型的執行個體。</span><span class="sxs-lookup"><span data-stu-id="f5e8f-123">For example, if the entity type is sealed and/or has no virtual properties then Create will just create an instance of the entity type.</span></span>  

## <a name="getting-the-actual-entity-type-from-a-proxy-type"></a><span data-ttu-id="f5e8f-124">從 proxy 類型中取得實際的實體類型</span><span class="sxs-lookup"><span data-stu-id="f5e8f-124">Getting the actual entity type from a proxy type</span></span>  

<span data-ttu-id="f5e8f-125">Proxy 型別有看起來像這樣的名稱：</span><span class="sxs-lookup"><span data-stu-id="f5e8f-125">Proxy types have names that look something like this:</span></span>  

<span data-ttu-id="f5e8f-126">System.Data.Entity.DynamicProxies.Blog_5E43C6C196972BF0754973E48C9C941092D86818CD94005E9A759B70BF6E48E6</span><span class="sxs-lookup"><span data-stu-id="f5e8f-126">System.Data.Entity.DynamicProxies.Blog_5E43C6C196972BF0754973E48C9C941092D86818CD94005E9A759B70BF6E48E6</span></span>  

<span data-ttu-id="f5e8f-127">您可以在此 proxy 使用針對類型從 ObjectContext GetObjectType 方法找到的實體類型。</span><span class="sxs-lookup"><span data-stu-id="f5e8f-127">You can find the entity type for this proxy type using the GetObjectType method from ObjectContext.</span></span> <span data-ttu-id="f5e8f-128">例如: </span><span class="sxs-lookup"><span data-stu-id="f5e8f-128">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    var entityType = ObjectContext.GetObjectType(blog.GetType());
}
```  

<span data-ttu-id="f5e8f-129">請注意，如果型別傳遞至 GetObjectType 仍然會傳回不是 proxy 類型的實體類型的實體類型的執行個體。</span><span class="sxs-lookup"><span data-stu-id="f5e8f-129">Note that if the type passed to GetObjectType is an instance of an entity type that is not a proxy type then the type of entity is still returned.</span></span> <span data-ttu-id="f5e8f-130">這表示您永遠可以使用這個方法，而不需要任何其他檢查類型是否 proxy 型別取得實際的實體類型。</span><span class="sxs-lookup"><span data-stu-id="f5e8f-130">This means you can always use this method to get the actual entity type without any other checking to see if the type is a proxy type or not.</span></span>  
