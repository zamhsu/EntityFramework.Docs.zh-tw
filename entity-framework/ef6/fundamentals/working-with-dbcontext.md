---
title: 使用 DbContext-EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: b0e6bddc-8a87-4d51-b1cb-7756df938c23
caps.latest.revision: 3
ms.openlocfilehash: 865c9883ce25f405a173791df4e46b98550cd41f
ms.sourcegitcommit: f05e7b62584cf228f17390bb086a61d505712e1b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2018
ms.locfileid: "39120227"
---
# <a name="working-with-dbcontext"></a><span data-ttu-id="5b6b4-102">使用 DbContext</span><span class="sxs-lookup"><span data-stu-id="5b6b4-102">Working with DbContext</span></span>

<span data-ttu-id="5b6b4-103">若要使用 Entity Framework 來查詢、 插入、 更新和刪除使用.NET 物件的資料，您必須先[建立模型](~/ef6/modeling/index.md)其對應的實體和您的模型資料庫中的資料表中所定義的關聯性。</span><span class="sxs-lookup"><span data-stu-id="5b6b4-103">In order to use Entity Framework to query, insert, update, and delete data using .NET objects, you first need to [Create a Model](~/ef6/modeling/index.md) which maps the entities and relationships that are defined in your model to tables in a database.</span></span>

<span data-ttu-id="5b6b4-104">一旦您有一個模型，您的應用程式互動的主要類別是`System.Data.Entity.DbContext`（通常稱為內容類別）。</span><span class="sxs-lookup"><span data-stu-id="5b6b4-104">Once you have a model, the primary class your application interacts with is `System.Data.Entity.DbContext` (often referred to as the context class).</span></span> <span data-ttu-id="5b6b4-105">您可以使用 DbContext，相關聯的模型：</span><span class="sxs-lookup"><span data-stu-id="5b6b4-105">You can use a DbContext associated to a model to:</span></span>
- <span data-ttu-id="5b6b4-106">撰寫及執行查詢</span><span class="sxs-lookup"><span data-stu-id="5b6b4-106">Write and execute queries</span></span>   
- <span data-ttu-id="5b6b4-107">做為實體物件具體化查詢結果</span><span class="sxs-lookup"><span data-stu-id="5b6b4-107">Materialize query results as entity objects</span></span>
- <span data-ttu-id="5b6b4-108">追蹤對這些物件所做的變更</span><span class="sxs-lookup"><span data-stu-id="5b6b4-108">Track changes that are made to those objects</span></span>
- <span data-ttu-id="5b6b4-109">保存回資料庫上的物件變更</span><span class="sxs-lookup"><span data-stu-id="5b6b4-109">Persist object changes back on the database</span></span>
- <span data-ttu-id="5b6b4-110">在記憶體中將物件繫結至 UI 控制項</span><span class="sxs-lookup"><span data-stu-id="5b6b4-110">Bind objects in memory to UI controls</span></span>

<span data-ttu-id="5b6b4-111">此頁面會提供有關如何管理內容類別的一些指引。</span><span class="sxs-lookup"><span data-stu-id="5b6b4-111">This page gives some guidance on how to manage the context class.</span></span>  

## <a name="defining-a-dbcontext-derived-class"></a><span data-ttu-id="5b6b4-112">定義衍生 DbContext 類別</span><span class="sxs-lookup"><span data-stu-id="5b6b4-112">Defining a DbContext derived class</span></span>  

<span data-ttu-id="5b6b4-113">若要使用內容的建議的方式是定義類別衍生自 DbContext，代表集合的內容中指定之實體的 DbSet 屬性公開 （expose）。</span><span class="sxs-lookup"><span data-stu-id="5b6b4-113">The recommended way to work with context is to define a class that derives from DbContext and exposes DbSet properties that represent collections of the specified entities in the context.</span></span> <span data-ttu-id="5b6b4-114">如果您正在使用 EF 設計工具，則會為您產生的內容。</span><span class="sxs-lookup"><span data-stu-id="5b6b4-114">If you are working with the EF Designer, the context will be generated for you.</span></span> <span data-ttu-id="5b6b4-115">如果您正在使用 Code First，您會通常內容自行撰寫。</span><span class="sxs-lookup"><span data-stu-id="5b6b4-115">If you are working with Code First, you will typically write the context yourself.</span></span>  

``` csharp
public class ProductContext : DbContext
{
    public DbSet<Category> Categories { get; set; }
    public DbSet<Product> Products { get; set; }
}
```  

<span data-ttu-id="5b6b4-116">內容之後，您會針對查詢中，加入 (使用`Add`或是`Attach`方法) 或移除 (使用`Remove`) 透過這些屬性的內容中的實體。</span><span class="sxs-lookup"><span data-stu-id="5b6b4-116">Once you have a context, you would query for, add (using `Add` or `Attach` methods ) or remove (using `Remove`) entities in the context through these properties.</span></span> <span data-ttu-id="5b6b4-117">存取`DbSet`內容物件上的屬性代表開始查詢，傳回指定之型別的所有實體。</span><span class="sxs-lookup"><span data-stu-id="5b6b4-117">Accessing a `DbSet` property on a context object represent a starting query that returns all entities of the specified type.</span></span> <span data-ttu-id="5b6b4-118">請注意，只存取屬性時，會執行查詢。</span><span class="sxs-lookup"><span data-stu-id="5b6b4-118">Note that just accessing a property will not execute the query.</span></span> <span data-ttu-id="5b6b4-119">執行查詢時：</span><span class="sxs-lookup"><span data-stu-id="5b6b4-119">A query is executed when:</span></span>  

- <span data-ttu-id="5b6b4-120">它是由 `foreach` (C#) 或 `For Each` (Visual Basic) 陳述式所列舉。</span><span class="sxs-lookup"><span data-stu-id="5b6b4-120">It is enumerated by a `foreach` (C#) or `For Each` (Visual Basic) statement.</span></span>  
- <span data-ttu-id="5b6b4-121">這類列舉集合作業所`ToArray`， `ToDictionary`，或`ToList`。</span><span class="sxs-lookup"><span data-stu-id="5b6b4-121">It is enumerated by a collection operation such as `ToArray`, `ToDictionary`, or `ToList`.</span></span>  
- <span data-ttu-id="5b6b4-122">LINQ 運算子，例如`First`或`Any`查詢的最外面部分中所指定。</span><span class="sxs-lookup"><span data-stu-id="5b6b4-122">LINQ operators such as `First` or `Any` are specified in the outermost part of the query.</span></span>  
- <span data-ttu-id="5b6b4-123">在呼叫下列方法的其中一個：`Load`擴充方法`DbEntityEntry.Reload`， `Database.ExecuteSqlCommand`，和`DbSet<T>.Find`，如果具有指定之索引鍵的實體找不到已載入內容中。</span><span class="sxs-lookup"><span data-stu-id="5b6b4-123">One of the following methods are called: the `Load` extension method, `DbEntityEntry.Reload`,  `Database.ExecuteSqlCommand`, and `DbSet<T>.Find`, if an entity with the specified key is not found already loaded in the context.</span></span>  

## <a name="lifetime"></a><span data-ttu-id="5b6b4-124">存留期</span><span class="sxs-lookup"><span data-stu-id="5b6b4-124">Lifetime</span></span>  

<span data-ttu-id="5b6b4-125">執行個體建立，而且當執行個體遭到處置或記憶體回收結束時，就會開始內容的存留期。</span><span class="sxs-lookup"><span data-stu-id="5b6b4-125">The lifetime of the context begins when the instance is created and ends when the instance is either disposed or garbage-collected.</span></span> <span data-ttu-id="5b6b4-126">使用**使用**如果您想要在區塊結尾處置內容所控制的所有資源。</span><span class="sxs-lookup"><span data-stu-id="5b6b4-126">Use **using** if you want all the resources that the context controls to be disposed at the end of the block.</span></span> <span data-ttu-id="5b6b4-127">當您使用**使用**，編譯器會自動建立 try/finally 區塊中的呼叫 dispose**最後**區塊。</span><span class="sxs-lookup"><span data-stu-id="5b6b4-127">When you use **using**, the compiler automatically creates a try/finally block and calls dispose in the **finally** block.</span></span>  

``` csharp
public void UseProducts()
{
    using (var context = new ProductContext())
    {     
        // Perform data access using the context
    }
}
```  

<span data-ttu-id="5b6b4-128">決定內容的存留期，以下是一些一般指導方針：</span><span class="sxs-lookup"><span data-stu-id="5b6b4-128">Here are some general guidelines when deciding on the lifetime of the context:</span></span>  

- <span data-ttu-id="5b6b4-129">當使用 Web 應用程式，請使用每個要求的內容執行個體。</span><span class="sxs-lookup"><span data-stu-id="5b6b4-129">When working with Web applications, use a context instance per request.</span></span>  
- <span data-ttu-id="5b6b4-130">當使用 Windows Presentation Foundation (WPF) 或 Windows Form，使用每個表單的內容執行個體。</span><span class="sxs-lookup"><span data-stu-id="5b6b4-130">When working with Windows Presentation Foundation (WPF) or Windows Forms, use a context instance per form.</span></span> <span data-ttu-id="5b6b4-131">這可讓您可以使用變更追蹤功能提供該內容。</span><span class="sxs-lookup"><span data-stu-id="5b6b4-131">This lets you use change-tracking functionality that context provides.</span></span>  
- <span data-ttu-id="5b6b4-132">如果內容執行個體由相依性插入容器，但它通常是處置內容容器的責任。</span><span class="sxs-lookup"><span data-stu-id="5b6b4-132">If the context instance is created by a dependency injection container, it is usually the responsibility of the container to dispose the context.</span></span>
- <span data-ttu-id="5b6b4-133">如果應用程式程式碼建立內容時，請記得處置內容時不再需要。</span><span class="sxs-lookup"><span data-stu-id="5b6b4-133">If the context is created in application code, remember to dispose of the context when it is no longer required.</span></span>  
- <span data-ttu-id="5b6b4-134">使用長時間執行的內容時請考慮下列各項：</span><span class="sxs-lookup"><span data-stu-id="5b6b4-134">When working with long-running context consider the following:</span></span>  
    - <span data-ttu-id="5b6b4-135">隨著您將更多的物件和其參考載入記憶體，內容的記憶體耗用量可能快速增加。</span><span class="sxs-lookup"><span data-stu-id="5b6b4-135">As you load more objects and their references into memory, the memory consumption of the context may increase rapidly.</span></span> <span data-ttu-id="5b6b4-136">這可能會導致效能問題。</span><span class="sxs-lookup"><span data-stu-id="5b6b4-136">This may cause performance issues.</span></span>  
    - <span data-ttu-id="5b6b4-137">內容不是安全執行緒，因此它不應該共用跨多個執行緒同時執行工作。</span><span class="sxs-lookup"><span data-stu-id="5b6b4-137">The context is not thread-safe, therefore it should not be shared across multiple threads doing work on it concurrently.</span></span>
    - <span data-ttu-id="5b6b4-138">如果例外狀況會導致內容處於無法復原的狀態，整個應用程式可能會終止。</span><span class="sxs-lookup"><span data-stu-id="5b6b4-138">If an exception causes the context to be in an unrecoverable state, the whole application may terminate.</span></span>  
    - <span data-ttu-id="5b6b4-139">當查詢資料與更新資料之間的時間差距增加時，遇到並行相關問題的機率也會增加。</span><span class="sxs-lookup"><span data-stu-id="5b6b4-139">The chances of running into concurrency-related issues increase as the gap between the time when the data is queried and updated grows.</span></span>  

## <a name="connections"></a><span data-ttu-id="5b6b4-140">連接</span><span class="sxs-lookup"><span data-stu-id="5b6b4-140">Connections</span></span>  

<span data-ttu-id="5b6b4-141">根據預設，內容會管理資料庫的連接。</span><span class="sxs-lookup"><span data-stu-id="5b6b4-141">By default, the context manages connections to the database.</span></span> <span data-ttu-id="5b6b4-142">內容會開啟，並視需要關閉連接。</span><span class="sxs-lookup"><span data-stu-id="5b6b4-142">The context opens and closes connections as needed.</span></span> <span data-ttu-id="5b6b4-143">比方說，內容開啟的連接執行查詢，並在處理所有結果集時，然後關閉連線。</span><span class="sxs-lookup"><span data-stu-id="5b6b4-143">For example, the context opens a connection to execute a query, and then closes the connection when all the result sets have been processed.</span></span>  

<span data-ttu-id="5b6b4-144">在某些情況下，您會想要擁有更大的控制權來控制何時開啟及關閉連接。</span><span class="sxs-lookup"><span data-stu-id="5b6b4-144">There are cases when you want to have more control over when the connection opens and closes.</span></span> <span data-ttu-id="5b6b4-145">例如，當使用 SQL Server Compact，通常建議維護個別開啟資料庫的連接來改善效能的應用程式的存留期。</span><span class="sxs-lookup"><span data-stu-id="5b6b4-145">For example, when working with SQL Server Compact, it is often recommended to maintain a separate open connection to the database for the lifetime of the application to improve performance.</span></span> <span data-ttu-id="5b6b4-146">您可以使用 `Connection` 屬性來手動管理這個處理序。</span><span class="sxs-lookup"><span data-stu-id="5b6b4-146">You can manage this process manually by using the `Connection` property.</span></span>  
