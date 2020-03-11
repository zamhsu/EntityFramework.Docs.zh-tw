---
title: 定義 DbSets-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 4528a509-ace7-4dfb-8065-1b833f5e03a0
ms.openlocfilehash: 045b22d2b9d26804948689dd7c9dd694baadda7e
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78419091"
---
# <a name="defining-dbsets"></a><span data-ttu-id="edc37-102">定義 DbSets</span><span class="sxs-lookup"><span data-stu-id="edc37-102">Defining DbSets</span></span>
<span data-ttu-id="edc37-103">使用 Code First 工作流程進行開發時，您會定義衍生的 DbCoNtext，以代表您的資料庫會話，並針對模型中的每個型別公開一個 DbSet。</span><span class="sxs-lookup"><span data-stu-id="edc37-103">When developing with the Code First workflow you define a derived DbContext that represents your session with the database and exposes a DbSet for each type in your model.</span></span> <span data-ttu-id="edc37-104">本主題涵蓋您可以定義 DbSet 屬性的各種方式。</span><span class="sxs-lookup"><span data-stu-id="edc37-104">This topic covers the various ways you can define the DbSet properties.</span></span>  

## <a name="dbcontext-with-dbset-properties"></a><span data-ttu-id="edc37-105">具有 DbSet 屬性的 DbCoNtext</span><span class="sxs-lookup"><span data-stu-id="edc37-105">DbContext with DbSet properties</span></span>  

<span data-ttu-id="edc37-106">Code First 範例中所顯示的常見案例，就是讓模型的實體類型具有公用自動 DbSet 屬性的 DbCoNtext。</span><span class="sxs-lookup"><span data-stu-id="edc37-106">The common case shown in Code First examples is to have a DbContext with public automatic DbSet properties for the entity types of your model.</span></span> <span data-ttu-id="edc37-107">例如：</span><span class="sxs-lookup"><span data-stu-id="edc37-107">For example:</span></span>  

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
}
```  

<span data-ttu-id="edc37-108">在 Code First 模式中使用時，這會將 Blog 和 Post 設定為實體類型，以及設定可從這些專案存取的其他類型。</span><span class="sxs-lookup"><span data-stu-id="edc37-108">When used in Code First mode, this will configure Blogs and Posts as entity types, as well as configuring other types reachable from these.</span></span> <span data-ttu-id="edc37-109">此外，DbCoNtext 會自動為每個屬性呼叫 setter，以設定適當 DbSet 的實例。</span><span class="sxs-lookup"><span data-stu-id="edc37-109">In addition DbContext will automatically call the setter for each of these properties to set an instance of the appropriate DbSet.</span></span>  

## <a name="dbcontext-with-idbset-properties"></a><span data-ttu-id="edc37-110">具有 Idbset 會屬性的 DbCoNtext</span><span class="sxs-lookup"><span data-stu-id="edc37-110">DbContext with IDbSet properties</span></span>  

<span data-ttu-id="edc37-111">在某些情況下，例如建立模擬或 fakes 時，使用介面宣告您的集合屬性會更有用。</span><span class="sxs-lookup"><span data-stu-id="edc37-111">There are situations, such as when creating mocks or fakes, where it is more useful to declare your set properties using an interface.</span></span> <span data-ttu-id="edc37-112">在這種情況下，Idbset 會介面可以用來取代 DbSet。</span><span class="sxs-lookup"><span data-stu-id="edc37-112">In such cases the IDbSet interface can be used in place of DbSet.</span></span> <span data-ttu-id="edc37-113">例如：</span><span class="sxs-lookup"><span data-stu-id="edc37-113">For example:</span></span>  

``` csharp
public class BloggingContext : DbContext
{
    public IDbSet<Blog> Blogs { get; set; }
    public IDbSet<Post> Posts { get; set; }
}
```  

<span data-ttu-id="edc37-114">此內容的運作方式與使用其 set 屬性之 DbSet 類別的內容完全相同。</span><span class="sxs-lookup"><span data-stu-id="edc37-114">This context works in exactly the same way as the context that uses the DbSet class for its set properties.</span></span>  

## <a name="dbcontext-with-read-only-set-properties"></a><span data-ttu-id="edc37-115">具有唯讀集屬性的 DbCoNtext</span><span class="sxs-lookup"><span data-stu-id="edc37-115">DbContext with read-only set properties</span></span>  

<span data-ttu-id="edc37-116">如果您不想要公開 DbSet 或 Idbset 會屬性的公用 setter，您可以改為建立唯讀屬性，並自行建立集合實例。</span><span class="sxs-lookup"><span data-stu-id="edc37-116">If you do not wish to expose public setters for your DbSet or IDbSet properties you can instead create read-only properties and create the set instances yourself.</span></span> <span data-ttu-id="edc37-117">例如：</span><span class="sxs-lookup"><span data-stu-id="edc37-117">For example:</span></span>  

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

<span data-ttu-id="edc37-118">請注意，DbCoNtext 會快取從 Set 方法傳回的 DbSet 實例，如此一來，每個屬性都會在每次呼叫時傳回相同的實例。</span><span class="sxs-lookup"><span data-stu-id="edc37-118">Note that DbContext caches the instance of DbSet returned from the Set method so that each of these properties will return the same instance every time it is called.</span></span>  

<span data-ttu-id="edc37-119">Code First 的實體類型探索在這裡的運作方式與使用公用 getter 和 setter 的屬性相同。</span><span class="sxs-lookup"><span data-stu-id="edc37-119">Discovery of entity types for Code First works in the same way here as it does for properties with public getters and setters.</span></span>  
