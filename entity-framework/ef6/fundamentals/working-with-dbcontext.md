---
title: 使用 DbCoNtext-EF6
description: 在 Entity Framework 6 中使用 DbCoNtext
author: divega
ms.date: 10/23/2016
ms.assetid: b0e6bddc-8a87-4d51-b1cb-7756df938c23
uid: ef6/fundamentals/working-with-dbcontext
ms.openlocfilehash: 7845d401cb0b8910cbfbba80eca2fd098c051b7d
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618219"
---
# <a name="working-with-dbcontext"></a><span data-ttu-id="f308d-103">使用 DbContext</span><span class="sxs-lookup"><span data-stu-id="f308d-103">Working with DbContext</span></span>

<span data-ttu-id="f308d-104">若要使用 Entity Framework 使用 .NET 物件來查詢、插入、更新和刪除資料，您必須先 [建立模型](xref:ef6/modeling/index) ，將模型中所定義的實體和關聯性對應至資料庫中的資料表。</span><span class="sxs-lookup"><span data-stu-id="f308d-104">In order to use Entity Framework to query, insert, update, and delete data using .NET objects, you first need to [Create a Model](xref:ef6/modeling/index) which maps the entities and relationships that are defined in your model to tables in a database.</span></span>

<span data-ttu-id="f308d-105">一旦有模型之後，您的應用程式所互動的主要類別 `System.Data.Entity.DbContext` (通常稱為內容類別) 。</span><span class="sxs-lookup"><span data-stu-id="f308d-105">Once you have a model, the primary class your application interacts with is `System.Data.Entity.DbContext` (often referred to as the context class).</span></span> <span data-ttu-id="f308d-106">您可以使用與模型相關聯的 DbCoNtext 來：</span><span class="sxs-lookup"><span data-stu-id="f308d-106">You can use a DbContext associated to a model to:</span></span>
- <span data-ttu-id="f308d-107">撰寫和執行查詢</span><span class="sxs-lookup"><span data-stu-id="f308d-107">Write and execute queries</span></span>   
- <span data-ttu-id="f308d-108">將查詢結果具體化為實體物件</span><span class="sxs-lookup"><span data-stu-id="f308d-108">Materialize query results as entity objects</span></span>
- <span data-ttu-id="f308d-109">追蹤對這些物件所做的變更</span><span class="sxs-lookup"><span data-stu-id="f308d-109">Track changes that are made to those objects</span></span>
- <span data-ttu-id="f308d-110">將物件變更保存回資料庫</span><span class="sxs-lookup"><span data-stu-id="f308d-110">Persist object changes back on the database</span></span>
- <span data-ttu-id="f308d-111">將記憶體中的物件系結至 UI 控制項</span><span class="sxs-lookup"><span data-stu-id="f308d-111">Bind objects in memory to UI controls</span></span>

<span data-ttu-id="f308d-112">此頁面提供有關如何管理內容類別的一些指引。</span><span class="sxs-lookup"><span data-stu-id="f308d-112">This page gives some guidance on how to manage the context class.</span></span>  

## <a name="defining-a-dbcontext-derived-class"></a><span data-ttu-id="f308d-113">定義 DbCoNtext 衍生類別</span><span class="sxs-lookup"><span data-stu-id="f308d-113">Defining a DbContext derived class</span></span>  

<span data-ttu-id="f308d-114">使用內容的建議方式是定義衍生自 DbCoNtext 的類別，並公開 DbSet 屬性，以表示內容中指定之實體的集合。</span><span class="sxs-lookup"><span data-stu-id="f308d-114">The recommended way to work with context is to define a class that derives from DbContext and exposes DbSet properties that represent collections of the specified entities in the context.</span></span> <span data-ttu-id="f308d-115">如果您使用 EF 設計工具，將會為您產生內容。</span><span class="sxs-lookup"><span data-stu-id="f308d-115">If you are working with the EF Designer, the context will be generated for you.</span></span> <span data-ttu-id="f308d-116">如果您正在使用 Code First，通常會自行撰寫內容。</span><span class="sxs-lookup"><span data-stu-id="f308d-116">If you are working with Code First, you will typically write the context yourself.</span></span>  

``` csharp
public class ProductContext : DbContext
{
    public DbSet<Category> Categories { get; set; }
    public DbSet<Product> Products { get; set; }
}
```  

<span data-ttu-id="f308d-117">一旦有內容之後，您就可以使用或方法來查詢、新增 (， `Add` `Attach` ) 或 `Remove` 透過這些屬性在內容中使用) 實體來移除 (。</span><span class="sxs-lookup"><span data-stu-id="f308d-117">Once you have a context, you would query for, add (using `Add` or `Attach` methods ) or remove (using `Remove`) entities in the context through these properties.</span></span> <span data-ttu-id="f308d-118">存取 `DbSet` 內容物件上的屬性代表會傳回指定型別之所有實體的起始查詢。</span><span class="sxs-lookup"><span data-stu-id="f308d-118">Accessing a `DbSet` property on a context object represent a starting query that returns all entities of the specified type.</span></span> <span data-ttu-id="f308d-119">請注意，只存取屬性不會執行查詢。</span><span class="sxs-lookup"><span data-stu-id="f308d-119">Note that just accessing a property will not execute the query.</span></span> <span data-ttu-id="f308d-120">當下列情況時，會執行查詢：</span><span class="sxs-lookup"><span data-stu-id="f308d-120">A query is executed when:</span></span>  

- <span data-ttu-id="f308d-121">它是由 `foreach` (C#) 或 `For Each` (Visual Basic) 陳述式所列舉。</span><span class="sxs-lookup"><span data-stu-id="f308d-121">It is enumerated by a `foreach` (C#) or `For Each` (Visual Basic) statement.</span></span>  
- <span data-ttu-id="f308d-122">它是由集合作業（例如、或）所列舉 `ToArray` `ToDictionary` `ToList` 。</span><span class="sxs-lookup"><span data-stu-id="f308d-122">It is enumerated by a collection operation such as `ToArray`, `ToDictionary`, or `ToList`.</span></span>  
- <span data-ttu-id="f308d-123">LINQ 運算子（例如 `First` 或） `Any` 是在查詢的最外面部分指定的。</span><span class="sxs-lookup"><span data-stu-id="f308d-123">LINQ operators such as `First` or `Any` are specified in the outermost part of the query.</span></span>  
- <span data-ttu-id="f308d-124">`Load` `DbEntityEntry.Reload` `Database.ExecuteSqlCommand` `DbSet<T>.Find` 如果找不到具有指定索引鍵的實體，則會呼叫下列其中一種方法：延伸方法：、和。</span><span class="sxs-lookup"><span data-stu-id="f308d-124">One of the following methods are called: the `Load` extension method, `DbEntityEntry.Reload`,  `Database.ExecuteSqlCommand`, and `DbSet<T>.Find`, if an entity with the specified key is not found already loaded in the context.</span></span>  

## <a name="lifetime"></a><span data-ttu-id="f308d-125">存留期</span><span class="sxs-lookup"><span data-stu-id="f308d-125">Lifetime</span></span>  

<span data-ttu-id="f308d-126">內容的存留期會在實例建立時開始，並在實例被處置或垃圾收集時結束。</span><span class="sxs-lookup"><span data-stu-id="f308d-126">The lifetime of the context begins when the instance is created and ends when the instance is either disposed or garbage-collected.</span></span> <span data-ttu-id="f308d-127">如果您想要在區塊結尾處置內容控制項的所有資源，請使用 **[使用]** 。</span><span class="sxs-lookup"><span data-stu-id="f308d-127">Use **using** if you want all the resources that the context controls to be disposed at the end of the block.</span></span> <span data-ttu-id="f308d-128">**使用時，編譯器**會自動建立 try/finally 區塊，並且在**finally**區塊中呼叫 dispose。</span><span class="sxs-lookup"><span data-stu-id="f308d-128">When you use **using**, the compiler automatically creates a try/finally block and calls dispose in the **finally** block.</span></span>  

``` csharp
public void UseProducts()
{
    using (var context = new ProductContext())
    {     
        // Perform data access using the context
    }
}
```  

<span data-ttu-id="f308d-129">以下是決定內容存留期時的一些一般指導方針：</span><span class="sxs-lookup"><span data-stu-id="f308d-129">Here are some general guidelines when deciding on the lifetime of the context:</span></span>  

- <span data-ttu-id="f308d-130">使用 Web 應用程式時，請針對每個要求使用內容實例。</span><span class="sxs-lookup"><span data-stu-id="f308d-130">When working with Web applications, use a context instance per request.</span></span>  
- <span data-ttu-id="f308d-131">使用 Windows Presentation Foundation (WPF) 或 Windows Forms 時，請使用每個表單的內容實例。</span><span class="sxs-lookup"><span data-stu-id="f308d-131">When working with Windows Presentation Foundation (WPF) or Windows Forms, use a context instance per form.</span></span> <span data-ttu-id="f308d-132">這可讓您使用內容提供的變更追蹤功能。</span><span class="sxs-lookup"><span data-stu-id="f308d-132">This lets you use change-tracking functionality that context provides.</span></span>  
- <span data-ttu-id="f308d-133">如果內容實例是由相依性插入容器所建立，則通常會負責處置內容的容器。</span><span class="sxs-lookup"><span data-stu-id="f308d-133">If the context instance is created by a dependency injection container, it is usually the responsibility of the container to dispose the context.</span></span>
- <span data-ttu-id="f308d-134">如果內容是在應用程式程式碼中建立的，請記得在不再需要內容時處置內容。</span><span class="sxs-lookup"><span data-stu-id="f308d-134">If the context is created in application code, remember to dispose of the context when it is no longer required.</span></span>  
- <span data-ttu-id="f308d-135">使用長時間執行的內容時，請考慮下列事項：</span><span class="sxs-lookup"><span data-stu-id="f308d-135">When working with long-running context consider the following:</span></span>  
    - <span data-ttu-id="f308d-136">當您將更多物件和其參考載入記憶體時，內容的記憶體耗用量可能會快速增加。</span><span class="sxs-lookup"><span data-stu-id="f308d-136">As you load more objects and their references into memory, the memory consumption of the context may increase rapidly.</span></span> <span data-ttu-id="f308d-137">這可能會導致效能問題。</span><span class="sxs-lookup"><span data-stu-id="f308d-137">This may cause performance issues.</span></span>  
    - <span data-ttu-id="f308d-138">內容不是安全線程，因此不應該跨多個執行緒同時執行工作。</span><span class="sxs-lookup"><span data-stu-id="f308d-138">The context is not thread-safe, therefore it should not be shared across multiple threads doing work on it concurrently.</span></span>
    - <span data-ttu-id="f308d-139">如果例外狀況造成內容處於無法復原的狀態，整個應用程式可能會終止。</span><span class="sxs-lookup"><span data-stu-id="f308d-139">If an exception causes the context to be in an unrecoverable state, the whole application may terminate.</span></span>  
    - <span data-ttu-id="f308d-140">當查詢資料與更新資料之間的時間差距增加時，遇到並行相關問題的機率也會增加。</span><span class="sxs-lookup"><span data-stu-id="f308d-140">The chances of running into concurrency-related issues increase as the gap between the time when the data is queried and updated grows.</span></span>  

## <a name="connections"></a><span data-ttu-id="f308d-141">連接</span><span class="sxs-lookup"><span data-stu-id="f308d-141">Connections</span></span>  

<span data-ttu-id="f308d-142">根據預設，內容會管理與資料庫的連接。</span><span class="sxs-lookup"><span data-stu-id="f308d-142">By default, the context manages connections to the database.</span></span> <span data-ttu-id="f308d-143">內容隨即開啟，並視需要關閉連接。</span><span class="sxs-lookup"><span data-stu-id="f308d-143">The context opens and closes connections as needed.</span></span> <span data-ttu-id="f308d-144">例如，內容會開啟連接來執行查詢，然後在處理所有結果集時關閉連接。</span><span class="sxs-lookup"><span data-stu-id="f308d-144">For example, the context opens a connection to execute a query, and then closes the connection when all the result sets have been processed.</span></span>  

<span data-ttu-id="f308d-145">在某些情況下，您會想要擁有更大的控制權來控制何時開啟及關閉連接。</span><span class="sxs-lookup"><span data-stu-id="f308d-145">There are cases when you want to have more control over when the connection opens and closes.</span></span> <span data-ttu-id="f308d-146">例如，使用 SQL Server Compact 時，通常建議在應用程式的存留期內針對資料庫維護個別的開啟連接，以改善效能。</span><span class="sxs-lookup"><span data-stu-id="f308d-146">For example, when working with SQL Server Compact, it is often recommended to maintain a separate open connection to the database for the lifetime of the application to improve performance.</span></span> <span data-ttu-id="f308d-147">您可以使用 `Connection` 屬性來手動管理這個處理序。</span><span class="sxs-lookup"><span data-stu-id="f308d-147">You can manage this process manually by using the `Connection` property.</span></span>  
