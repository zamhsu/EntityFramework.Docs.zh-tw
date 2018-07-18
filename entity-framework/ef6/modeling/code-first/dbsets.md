---
title: 定義 DbSets-EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 4528a509-ace7-4dfb-8065-1b833f5e03a0
caps.latest.revision: 3
ms.openlocfilehash: 8a495c6ce74d9a346a84b0e10fb28395f4dce07b
ms.sourcegitcommit: 00cb52625b57c1ea339ded1454179fe89b6bcfea
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/16/2018
ms.locfileid: "39121726"
---
# <a name="defining-dbsets"></a><span data-ttu-id="d3fde-102">定義 DbSets</span><span class="sxs-lookup"><span data-stu-id="d3fde-102">Defining DbSets</span></span>
<span data-ttu-id="d3fde-103">使用 Code First 的工作流程開發時您會定義衍生的 DbContext，其代表您的工作階段與資料庫，並會公開您的模型中每個類型的 DbSet。</span><span class="sxs-lookup"><span data-stu-id="d3fde-103">When developing with the Code First workflow you define a derived DbContext that represents your session with the database and exposes a DbSet for each type in your model.</span></span> <span data-ttu-id="d3fde-104">本主題涵蓋您可以定義 DbSet 屬性的各種方式。</span><span class="sxs-lookup"><span data-stu-id="d3fde-104">This topic covers the various ways you can define the DbSet properties.</span></span>  

## <a name="dbcontext-with-dbset-properties"></a><span data-ttu-id="d3fde-105">DbContext DbSet 屬性</span><span class="sxs-lookup"><span data-stu-id="d3fde-105">DbContext with DbSet properties</span></span>  

<span data-ttu-id="d3fde-106">第一個程式碼範例所示的常見案例是模型的將 DbContext 具有公用自動針對您實體類型的 DbSet 屬性。</span><span class="sxs-lookup"><span data-stu-id="d3fde-106">The common case shown in Code First examples is to have a DbContext with public automatic DbSet properties for the entity types of your model.</span></span> <span data-ttu-id="d3fde-107">例如: </span><span class="sxs-lookup"><span data-stu-id="d3fde-107">For example:</span></span>  

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
}
```  

<span data-ttu-id="d3fde-108">使用 Code First 模式中，這會設定為實體類型，以及設定可從這些其他類型的部落格和文章。</span><span class="sxs-lookup"><span data-stu-id="d3fde-108">When used in Code First mode, this will configure Blogs and Posts as entity types, as well as configuring other types reachable from these.</span></span> <span data-ttu-id="d3fde-109">此外 DbContext 會自動為每個這些屬性，以執行個體設定為適當的 DbSet 呼叫 setter。</span><span class="sxs-lookup"><span data-stu-id="d3fde-109">In addition DbContext will automatically call the setter for each of these properties to set an instance of the appropriate DbSet.</span></span>  

## <a name="dbcontext-with-idbset-properties"></a><span data-ttu-id="d3fde-110">DbContext IDbSet 屬性</span><span class="sxs-lookup"><span data-stu-id="d3fde-110">DbContext with IDbSet properties</span></span>  

<span data-ttu-id="d3fde-111">有些情況，例如當建立模擬或 fakes，很多有用來宣告您的設定屬性使用的介面。</span><span class="sxs-lookup"><span data-stu-id="d3fde-111">There are situations, such as when creating mocks or fakes, where it is more useful to declare your set properties using an interface.</span></span> <span data-ttu-id="d3fde-112">在此情況下 IDbSet 介面可用以取代 DbSet。</span><span class="sxs-lookup"><span data-stu-id="d3fde-112">In such cases the IDbSet interface can be used in place of DbSet.</span></span> <span data-ttu-id="d3fde-113">例如: </span><span class="sxs-lookup"><span data-stu-id="d3fde-113">For example:</span></span>  

``` csharp
public class BloggingContext : DbContext
{
    public IDbSet<Blog> Blogs { get; set; }
    public IDbSet<Post> Posts { get; set; }
}
```  

<span data-ttu-id="d3fde-114">此內容適用於使用 DbSet 類別，針對其設定屬性的內容完全相同的方式。</span><span class="sxs-lookup"><span data-stu-id="d3fde-114">This context works in exactly the same way as the context that uses the DbSet class for its set properties.</span></span>  

## <a name="dbcontext-with-read-only-set-properties"></a><span data-ttu-id="d3fde-115">使用唯讀的 DbContext 設定屬性</span><span class="sxs-lookup"><span data-stu-id="d3fde-115">DbContext with read-only set properties</span></span>  

<span data-ttu-id="d3fde-116">如果您不想公開公用 setter DbSet 或 IDbSet 屬性可以改為建立唯讀屬性，並自行建立集執行個體。</span><span class="sxs-lookup"><span data-stu-id="d3fde-116">If you do not wish to expose public setters for your DbSet or IDbSet properties you can instead create read-only properties and create the set instances yourself.</span></span> <span data-ttu-id="d3fde-117">例如: </span><span class="sxs-lookup"><span data-stu-id="d3fde-117">For example:</span></span>  

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs
    {
        get { return Set<Blog>(); }
    }

    public DbSet<Post> Posts
    {
        get { return Set<Post>(); }
    }
}
```  

<span data-ttu-id="d3fde-118">請注意，DbContext 會快取的 DbSet 傳回從 Set 方法，以便每次呼叫時，每個屬性會傳回相同的執行個體的執行個體。</span><span class="sxs-lookup"><span data-stu-id="d3fde-118">Note that DbContext caches the instance of DbSet returned from the Set method so that each of these properties will return the same instance every time it is called.</span></span>  

<span data-ttu-id="d3fde-119">探索相同的方式中的第一個程式碼運作的實體類型一樣對具有公用 getter 和 setter 的屬性。</span><span class="sxs-lookup"><span data-stu-id="d3fde-119">Discovery of entity types for Code First works in the same way here as it does for properties with public getters and setters.</span></span>  
