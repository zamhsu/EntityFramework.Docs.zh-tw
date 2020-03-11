---
title: 使用 proxy-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 869ee4dc-06f1-471d-8e0e-0a1a2bc59c30
ms.openlocfilehash: 8f7d2e8b41ece28efe8d1df3b0679e6e4510d64a
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78419336"
---
# <a name="working-with-proxies"></a><span data-ttu-id="259c0-102">使用 proxy</span><span class="sxs-lookup"><span data-stu-id="259c0-102">Working with proxies</span></span>
<span data-ttu-id="259c0-103">建立 POCO 實體類型的實例時，Entity Framework 通常會建立動態產生之衍生類型的實例，作為實體的 proxy。</span><span class="sxs-lookup"><span data-stu-id="259c0-103">When creating instances of POCO entity types, Entity Framework often creates instances of a dynamically generated derived type that acts as a proxy for the entity.</span></span> <span data-ttu-id="259c0-104">此 proxy 會覆寫實體的某些虛擬屬性，以插入在存取屬性時自動執行動作的勾點。</span><span class="sxs-lookup"><span data-stu-id="259c0-104">This proxy overrides some virtual properties of the entity to insert hooks for performing actions automatically when the property is accessed.</span></span> <span data-ttu-id="259c0-105">例如，這項機制是用來支援延遲載入關聯性。</span><span class="sxs-lookup"><span data-stu-id="259c0-105">For example, this mechanism is used to support lazy loading of relationships.</span></span> <span data-ttu-id="259c0-106">本主題所示範的技巧同樣適用於使用 Code First 和 EF 設計工具所建立的模型。</span><span class="sxs-lookup"><span data-stu-id="259c0-106">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

## <a name="disabling-proxy-creation"></a><span data-ttu-id="259c0-107">停用 proxy 建立</span><span class="sxs-lookup"><span data-stu-id="259c0-107">Disabling proxy creation</span></span>  

<span data-ttu-id="259c0-108">有時候，防止 Entity Framework 建立 proxy 實例會很有用。</span><span class="sxs-lookup"><span data-stu-id="259c0-108">Sometimes it is useful to prevent Entity Framework from creating proxy instances.</span></span> <span data-ttu-id="259c0-109">例如，序列化非 proxy 實例會比序列化 proxy 實例簡單得多。</span><span class="sxs-lookup"><span data-stu-id="259c0-109">For example, serializing non-proxy instances is considerably easier than serializing proxy instances.</span></span> <span data-ttu-id="259c0-110">藉由清除 ProxyCreationEnabled 旗標，可以關閉 Proxy 建立。</span><span class="sxs-lookup"><span data-stu-id="259c0-110">Proxy creation can be turned off by clearing the ProxyCreationEnabled flag.</span></span> <span data-ttu-id="259c0-111">您可以執行這項操作的其中一個地方是在您的內容的函式中。</span><span class="sxs-lookup"><span data-stu-id="259c0-111">One place you could do this is in the constructor of your context.</span></span> <span data-ttu-id="259c0-112">例如：</span><span class="sxs-lookup"><span data-stu-id="259c0-112">For example:</span></span>  

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

<span data-ttu-id="259c0-113">請注意，如果沒有 proxy 要執行的類型，則 EF 不會建立 proxy。</span><span class="sxs-lookup"><span data-stu-id="259c0-113">Note that the EF will not create proxies for types where there is nothing for the proxy to do.</span></span> <span data-ttu-id="259c0-114">這表示您也可以藉由擁有密封的類型和/或沒有虛擬屬性來避免 proxy。</span><span class="sxs-lookup"><span data-stu-id="259c0-114">This means that you can also avoid proxies by having types that are sealed and/or have no virtual properties.</span></span>  

## <a name="explicitly-creating-an-instance-of-a-proxy"></a><span data-ttu-id="259c0-115">明確建立 proxy 的實例</span><span class="sxs-lookup"><span data-stu-id="259c0-115">Explicitly creating an instance of a proxy</span></span>  

<span data-ttu-id="259c0-116">如果您使用 new 運算子建立實體的實例，則不會建立 proxy 實例。</span><span class="sxs-lookup"><span data-stu-id="259c0-116">A proxy instance will not be created if you create an instance of an entity using the new operator.</span></span> <span data-ttu-id="259c0-117">這可能不是問題，但如果您需要建立 proxy 實例（例如，讓消極式載入或 proxy 變更追蹤能夠正常執行），您可以使用 DbSet 的 Create 方法來這麼做。</span><span class="sxs-lookup"><span data-stu-id="259c0-117">This may not be a problem, but if you need to create a proxy instance (for example, so that lazy loading or proxy change tracking will work) then you can do so using the Create method of DbSet.</span></span> <span data-ttu-id="259c0-118">例如：</span><span class="sxs-lookup"><span data-stu-id="259c0-118">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Create();
}
```  

<span data-ttu-id="259c0-119">如果您想要建立衍生實體類型的實例，則可以使用 Create 的泛型版本。</span><span class="sxs-lookup"><span data-stu-id="259c0-119">The generic version of Create can be used if you want to create an instance of a derived entity type.</span></span> <span data-ttu-id="259c0-120">例如：</span><span class="sxs-lookup"><span data-stu-id="259c0-120">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var admin = context.Users.Create<Administrator>();
}
```  

<span data-ttu-id="259c0-121">請注意，Create 方法不會將建立的實體加入或附加至內容。</span><span class="sxs-lookup"><span data-stu-id="259c0-121">Note that the Create method does not add or attach the created entity to the context.</span></span>  

<span data-ttu-id="259c0-122">請注意，如果建立實體的 proxy 類型不會有任何值，Create 方法只會建立實體類型本身的實例，因為它不會執行任何動作。</span><span class="sxs-lookup"><span data-stu-id="259c0-122">Note that the Create method will just create an instance of the entity type itself if creating a proxy type for the entity would have no value because it would not do anything.</span></span> <span data-ttu-id="259c0-123">例如，如果實體類型是密封的，而且（或沒有虛擬屬性），則 Create 只會建立實體類型的實例。</span><span class="sxs-lookup"><span data-stu-id="259c0-123">For example, if the entity type is sealed and/or has no virtual properties then Create will just create an instance of the entity type.</span></span>  

## <a name="getting-the-actual-entity-type-from-a-proxy-type"></a><span data-ttu-id="259c0-124">從 proxy 類型取得實際實體類型</span><span class="sxs-lookup"><span data-stu-id="259c0-124">Getting the actual entity type from a proxy type</span></span>  

<span data-ttu-id="259c0-125">Proxy 類型的名稱如下所示：</span><span class="sxs-lookup"><span data-stu-id="259c0-125">Proxy types have names that look something like this:</span></span>  

<span data-ttu-id="259c0-126">DynamicProxies. Entity. Blog_5E43C6C196972BF0754973E48C9C941092D86818CD94005E9A759B70BF6E48E6</span><span class="sxs-lookup"><span data-stu-id="259c0-126">System.Data.Entity.DynamicProxies.Blog_5E43C6C196972BF0754973E48C9C941092D86818CD94005E9A759B70BF6E48E6</span></span>  

<span data-ttu-id="259c0-127">您可以使用 ObjectCoNtext 的 GetObjectType 方法，來尋找此 proxy 類型的實體類型。</span><span class="sxs-lookup"><span data-stu-id="259c0-127">You can find the entity type for this proxy type using the GetObjectType method from ObjectContext.</span></span> <span data-ttu-id="259c0-128">例如：</span><span class="sxs-lookup"><span data-stu-id="259c0-128">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    var entityType = ObjectContext.GetObjectType(blog.GetType());
}
```  

<span data-ttu-id="259c0-129">請注意，如果傳遞至 GetObjectType 的類型是不是 proxy 類型之實體類型的實例，則仍會傳回實體的類型。</span><span class="sxs-lookup"><span data-stu-id="259c0-129">Note that if the type passed to GetObjectType is an instance of an entity type that is not a proxy type then the type of entity is still returned.</span></span> <span data-ttu-id="259c0-130">這表示您一律可以使用這個方法來取得實際實體類型，而不需要任何其他檢查，以查看該類型是否為 proxy 類型。</span><span class="sxs-lookup"><span data-stu-id="259c0-130">This means you can always use this method to get the actual entity type without any other checking to see if the type is a proxy type or not.</span></span>  
