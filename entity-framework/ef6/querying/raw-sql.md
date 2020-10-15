---
title: 原始 SQL 查詢-EF6
description: Entity Framework 6 中的原始 SQL 查詢
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/querying/raw-sql
ms.openlocfilehash: da813ede818b24a5e7930202bfa761d65e4e6b72
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064714"
---
# <a name="raw-sql-queries-ef6"></a><span data-ttu-id="38f75-103"> (EF6) 的原始 SQL 查詢</span><span class="sxs-lookup"><span data-stu-id="38f75-103">Raw SQL Queries (EF6)</span></span>

<span data-ttu-id="38f75-104">Entity Framework 可讓您使用 LINQ 搭配您的實體類別來進行查詢。</span><span class="sxs-lookup"><span data-stu-id="38f75-104">Entity Framework allows you to query using LINQ with your entity classes.</span></span> <span data-ttu-id="38f75-105">不過，有時候您可能會想要直接對資料庫執行使用原始 SQL 的查詢。</span><span class="sxs-lookup"><span data-stu-id="38f75-105">However, there may be times that you want to run queries using raw SQL directly against the database.</span></span> <span data-ttu-id="38f75-106">這包括呼叫預存程式，對於目前不支援對應至預存程式的 Code First 模型很有説明。</span><span class="sxs-lookup"><span data-stu-id="38f75-106">This includes calling stored procedures, which can be helpful for Code First models that currently do not support mapping to stored procedures.</span></span> <span data-ttu-id="38f75-107">本主題所示範的技巧同樣適用於使用 Code First 和 EF 設計工具所建立的模型。</span><span class="sxs-lookup"><span data-stu-id="38f75-107">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

## <a name="writing-sql-queries-for-entities"></a><span data-ttu-id="38f75-108">撰寫實體的 SQL 查詢</span><span class="sxs-lookup"><span data-stu-id="38f75-108">Writing SQL queries for entities</span></span>  

<span data-ttu-id="38f75-109">DbSet 上的 SqlQuery 方法可讓您撰寫可傳回實體實例的原始 SQL 查詢。</span><span class="sxs-lookup"><span data-stu-id="38f75-109">The SqlQuery method on DbSet allows a raw SQL query to be written that will return entity instances.</span></span> <span data-ttu-id="38f75-110">傳回的物件將會被內容追蹤，就像 LINQ 查詢所傳回的一樣。</span><span class="sxs-lookup"><span data-stu-id="38f75-110">The returned objects will be tracked by the context just as they would be if they were returned by a LINQ query.</span></span> <span data-ttu-id="38f75-111">例如︰</span><span class="sxs-lookup"><span data-stu-id="38f75-111">For example:</span></span>  

``` csharp  
using (var context = new BloggingContext())
{
    var blogs = context.Blogs.SqlQuery("SELECT * FROM dbo.Blogs").ToList();
}
```  

<span data-ttu-id="38f75-112">請注意，就像 LINQ 查詢一樣，查詢不會執行，直到列舉結果為止（在上述範例中，這是透過呼叫 ToList 來完成）。</span><span class="sxs-lookup"><span data-stu-id="38f75-112">Note that, just as for LINQ queries, the query is not executed until the results are enumerated—in the example above this is done with the call to ToList.</span></span>  

<span data-ttu-id="38f75-113">因為有兩個原因，所以撰寫原始 SQL 查詢時，應該小心執行。</span><span class="sxs-lookup"><span data-stu-id="38f75-113">Care should be taken whenever raw SQL queries are written for two reasons.</span></span> <span data-ttu-id="38f75-114">首先，應該撰寫查詢，以確保它只會傳回真正是所要求類型的實體。</span><span class="sxs-lookup"><span data-stu-id="38f75-114">First, the query should be written to ensure that it only returns entities that are really of the requested type.</span></span> <span data-ttu-id="38f75-115">例如，使用繼承之類的功能時，很容易就能撰寫查詢來建立錯誤的 CLR 型別中的實體。</span><span class="sxs-lookup"><span data-stu-id="38f75-115">For example, when using features such as inheritance it is easy to write a query that will create entities that are of the wrong CLR type.</span></span>  

<span data-ttu-id="38f75-116">其次，某些類型的原始 SQL 查詢會公開潛在的安全性風險，特別是針對 SQL 插入式攻擊。</span><span class="sxs-lookup"><span data-stu-id="38f75-116">Second, some types of raw SQL query expose potential security risks, especially around SQL injection attacks.</span></span> <span data-ttu-id="38f75-117">請確定您以正確的方式使用查詢中的參數，以防範這類攻擊。</span><span class="sxs-lookup"><span data-stu-id="38f75-117">Make sure that you use parameters in your query in the correct way to guard against such attacks.</span></span>  

### <a name="loading-entities-from-stored-procedures"></a><span data-ttu-id="38f75-118">從預存程式載入實體</span><span class="sxs-lookup"><span data-stu-id="38f75-118">Loading entities from stored procedures</span></span>  

<span data-ttu-id="38f75-119">您可以使用 DbSet SqlQuery，從預存程式的結果載入實體。</span><span class="sxs-lookup"><span data-stu-id="38f75-119">You can use DbSet.SqlQuery to load entities from the results of a stored procedure.</span></span> <span data-ttu-id="38f75-120">例如，下列程式碼會呼叫 dbo。資料庫中的 GetBlogs 程式：</span><span class="sxs-lookup"><span data-stu-id="38f75-120">For example, the following code calls the dbo.GetBlogs procedure in the database:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blogs = context.Blogs.SqlQuery("dbo.GetBlogs").ToList();
}
```  

<span data-ttu-id="38f75-121">您也可以使用下列語法，將參數傳遞至預存程式：</span><span class="sxs-lookup"><span data-stu-id="38f75-121">You can also pass parameters to a stored procedure using the following syntax:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blogId = 1;

    var blogs = context.Blogs.SqlQuery("dbo.GetBlogById @p0", blogId).Single();
}
```  

## <a name="writing-sql-queries-for-non-entity-types"></a><span data-ttu-id="38f75-122">撰寫非實體類型的 SQL 查詢</span><span class="sxs-lookup"><span data-stu-id="38f75-122">Writing SQL queries for non-entity types</span></span>  

<span data-ttu-id="38f75-123">您可以使用 Database 類別上的 SqlQuery 方法，建立傳回任何型別實例的 SQL 查詢（包括基本類型）。</span><span class="sxs-lookup"><span data-stu-id="38f75-123">A SQL query returning instances of any type, including primitive types, can be created using the SqlQuery method on the Database class.</span></span> <span data-ttu-id="38f75-124">例如︰</span><span class="sxs-lookup"><span data-stu-id="38f75-124">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blogNames = context.Database.SqlQuery<string>(
                       "SELECT Name FROM dbo.Blogs").ToList();
}
```  

<span data-ttu-id="38f75-125">即使物件是實體類型的實例，內容也永遠不會追蹤從資料庫上的 SqlQuery 傳回的結果。</span><span class="sxs-lookup"><span data-stu-id="38f75-125">The results returned from SqlQuery on Database will never be tracked by the context even if the objects are instances of an entity type.</span></span>  

## <a name="sending-raw-commands-to-the-database"></a><span data-ttu-id="38f75-126">傳送原始命令至資料庫</span><span class="sxs-lookup"><span data-stu-id="38f75-126">Sending raw commands to the database</span></span>  

<span data-ttu-id="38f75-127">您可以使用資料庫上的 ExecuteSqlCommand 方法，將非查詢命令傳送至資料庫。</span><span class="sxs-lookup"><span data-stu-id="38f75-127">Non-query commands can be sent to the database using the ExecuteSqlCommand method on Database.</span></span> <span data-ttu-id="38f75-128">例如︰</span><span class="sxs-lookup"><span data-stu-id="38f75-128">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    context.Database.ExecuteSqlCommand(
        "UPDATE dbo.Blogs SET Name = 'Another Name' WHERE BlogId = 1");
}
```  

<span data-ttu-id="38f75-129">請注意，在從資料庫載入或重載實體之前，使用 ExecuteSqlCommand 對資料庫中的資料所做的任何變更都是內容中的不透明。</span><span class="sxs-lookup"><span data-stu-id="38f75-129">Note that any changes made to data in the database using ExecuteSqlCommand are opaque to the context until entities are loaded or reloaded from the database.</span></span>  

### <a name="output-parameters"></a><span data-ttu-id="38f75-130">輸出參數</span><span class="sxs-lookup"><span data-stu-id="38f75-130">Output Parameters</span></span>  

<span data-ttu-id="38f75-131">如果使用 output 參數，則在結果完全讀取之前，其值將無法使用。</span><span class="sxs-lookup"><span data-stu-id="38f75-131">If output parameters are used, their values will not be available until the results have been read completely.</span></span> <span data-ttu-id="38f75-132">這是因為 DbDataReader 的基礎行為，請參閱 [使用 DataReader 抓取資料](https://go.microsoft.com/fwlink/?LinkID=398589) 以取得更多詳細資料。</span><span class="sxs-lookup"><span data-stu-id="38f75-132">This is due to the underlying behavior of DbDataReader, see [Retrieving Data Using a DataReader](https://go.microsoft.com/fwlink/?LinkID=398589) for more details.</span></span>  
