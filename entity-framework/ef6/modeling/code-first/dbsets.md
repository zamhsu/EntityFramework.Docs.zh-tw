---
title: 定義 DbSets-EF6
description: 在 Entity Framework 6 中定義 DbSets
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/code-first/dbsets
ms.openlocfilehash: 49ffe3ddb8cd4f7c414d7d45d108405b88a0115e
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073947"
---
# <a name="defining-dbsets"></a><span data-ttu-id="8748f-103">定義 DbSets</span><span class="sxs-lookup"><span data-stu-id="8748f-103">Defining DbSets</span></span>
<span data-ttu-id="8748f-104">使用 Code First 工作流程進行開發時，您會定義衍生的 DbCoNtext，以代表您的會話與資料庫，並針對模型中的每個類型公開 DbSet。</span><span class="sxs-lookup"><span data-stu-id="8748f-104">When developing with the Code First workflow you define a derived DbContext that represents your session with the database and exposes a DbSet for each type in your model.</span></span> <span data-ttu-id="8748f-105">本主題涵蓋您可以定義 DbSet 屬性的各種方式。</span><span class="sxs-lookup"><span data-stu-id="8748f-105">This topic covers the various ways you can define the DbSet properties.</span></span>  

## <a name="dbcontext-with-dbset-properties"></a><span data-ttu-id="8748f-106">具有 DbSet 屬性的 DbCoNtext</span><span class="sxs-lookup"><span data-stu-id="8748f-106">DbContext with DbSet properties</span></span>  

<span data-ttu-id="8748f-107">Code First 範例中所顯示的常見案例是針對模型的實體類型，具有公用自動 DbSet 屬性的 DbCoNtext。</span><span class="sxs-lookup"><span data-stu-id="8748f-107">The common case shown in Code First examples is to have a DbContext with public automatic DbSet properties for the entity types of your model.</span></span> <span data-ttu-id="8748f-108">例如：</span><span class="sxs-lookup"><span data-stu-id="8748f-108">For example:</span></span>  

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
}
```  

<span data-ttu-id="8748f-109">在 Code First 模式中使用時，這會將 Blog 和 Post 設定為實體類型，以及設定可從這些類型存取的其他類型。</span><span class="sxs-lookup"><span data-stu-id="8748f-109">When used in Code First mode, this will configure Blogs and Posts as entity types, as well as configuring other types reachable from these.</span></span> <span data-ttu-id="8748f-110">此外，DbCoNtext 會自動為每個屬性呼叫 setter，以設定適當 DbSet 的實例。</span><span class="sxs-lookup"><span data-stu-id="8748f-110">In addition DbContext will automatically call the setter for each of these properties to set an instance of the appropriate DbSet.</span></span>  

## <a name="dbcontext-with-idbset-properties"></a><span data-ttu-id="8748f-111">具有 Idbset 會屬性的 DbCoNtext</span><span class="sxs-lookup"><span data-stu-id="8748f-111">DbContext with IDbSet properties</span></span>  

<span data-ttu-id="8748f-112">在某些情況下，例如在建立模擬或 fakes 時，使用介面宣告設定的屬性會更有用。</span><span class="sxs-lookup"><span data-stu-id="8748f-112">There are situations, such as when creating mocks or fakes, where it is more useful to declare your set properties using an interface.</span></span> <span data-ttu-id="8748f-113">在這種情況下，Idbset 會介面可用來取代 DbSet。</span><span class="sxs-lookup"><span data-stu-id="8748f-113">In such cases the IDbSet interface can be used in place of DbSet.</span></span> <span data-ttu-id="8748f-114">例如：</span><span class="sxs-lookup"><span data-stu-id="8748f-114">For example:</span></span>  

``` csharp
public class BloggingContext : DbContext
{
    public IDbSet<Blog> Blogs { get; set; }
    public IDbSet<Post> Posts { get; set; }
}
```  

<span data-ttu-id="8748f-115">此內容的運作方式與使用 DbSet 類別做為其 set 屬性的內容完全相同。</span><span class="sxs-lookup"><span data-stu-id="8748f-115">This context works in exactly the same way as the context that uses the DbSet class for its set properties.</span></span>  

## <a name="dbcontext-with-read-only-set-properties"></a><span data-ttu-id="8748f-116">具有唯讀設定屬性的 DbCoNtext</span><span class="sxs-lookup"><span data-stu-id="8748f-116">DbContext with read-only set properties</span></span>  

<span data-ttu-id="8748f-117">如果您不想要對 DbSet 或 Idbset 會屬性公開公用 setter，可以改為建立唯讀屬性，並自行建立集合實例。</span><span class="sxs-lookup"><span data-stu-id="8748f-117">If you do not wish to expose public setters for your DbSet or IDbSet properties you can instead create read-only properties and create the set instances yourself.</span></span> <span data-ttu-id="8748f-118">例如：</span><span class="sxs-lookup"><span data-stu-id="8748f-118">For example:</span></span>  

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

<span data-ttu-id="8748f-119">請注意，DbCoNtext 會快取從 Set 方法傳回的 DbSet 實例，讓每個屬性每次呼叫時都會傳回相同的實例。</span><span class="sxs-lookup"><span data-stu-id="8748f-119">Note that DbContext caches the instance of DbSet returned from the Set method so that each of these properties will return the same instance every time it is called.</span></span>  

<span data-ttu-id="8748f-120">Code First 的實體類型探索的運作方式，與使用公用 getter 和 setter 的屬性相同。</span><span class="sxs-lookup"><span data-stu-id="8748f-120">Discovery of entity types for Code First works in the same way here as it does for properties with public getters and setters.</span></span>  
