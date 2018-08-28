---
title: 原始 SQL 查詢-EF6
author: divega
ms.date: 2016-10-23
ms.assetid: 9e1ee76e-2499-408c-81e8-9b6c5d1945a0
ms.openlocfilehash: 99893ca1c634ce6f2e4cf9dcb70b1a1e43532c60
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42995730"
---
# <a name="raw-sql-queries"></a><span data-ttu-id="5b4a7-102">原始 SQL 查詢</span><span class="sxs-lookup"><span data-stu-id="5b4a7-102">Raw SQL Queries</span></span>
<span data-ttu-id="5b4a7-103">Entity Framework 可讓您與您的實體類別中使用 LINQ 進行查詢。</span><span class="sxs-lookup"><span data-stu-id="5b4a7-103">Entity Framework allows you to query using LINQ with your entity classes.</span></span> <span data-ttu-id="5b4a7-104">不過，可能有您想要使用原始的 SQL 直接對資料庫執行查詢的時間。</span><span class="sxs-lookup"><span data-stu-id="5b4a7-104">However, there may be times that you want to run queries using raw SQL directly against the database.</span></span> <span data-ttu-id="5b4a7-105">這包括呼叫預存程序，可以幫助您目前不支援對應至預存程序的 Code First 模型。</span><span class="sxs-lookup"><span data-stu-id="5b4a7-105">This includes calling stored procedures, which can be helpful for Code First models that currently do not support mapping to stored procedures.</span></span> <span data-ttu-id="5b4a7-106">本主題所示範的技巧同樣適用於使用 Code First 和 EF 設計工具所建立的模型。</span><span class="sxs-lookup"><span data-stu-id="5b4a7-106">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

## <a name="writing-sql-queries-for-entities"></a><span data-ttu-id="5b4a7-107">撰寫 SQL 查詢實體</span><span class="sxs-lookup"><span data-stu-id="5b4a7-107">Writing SQL queries for entities</span></span>  

<span data-ttu-id="5b4a7-108">DbSet SqlQuery 方法可讓原始的 SQL 查詢，以寫入，會傳回實體執行個體。</span><span class="sxs-lookup"><span data-stu-id="5b4a7-108">The SqlQuery method on DbSet allows a raw SQL query to be written that will return entity instances.</span></span> <span data-ttu-id="5b4a7-109">內容會追蹤傳回的物件，就如同它們由 LINQ 查詢，其方式是。</span><span class="sxs-lookup"><span data-stu-id="5b4a7-109">The returned objects will be tracked by the context just as they would be if they were returned by a LINQ query.</span></span> <span data-ttu-id="5b4a7-110">例如: </span><span class="sxs-lookup"><span data-stu-id="5b4a7-110">For example:</span></span>  

``` csharp  
using (var context = new BloggingContext())
{
    var blogs = context.Blogs.SqlQuery("SELECT * FROM dbo.Blogs").ToList();
}
```  

<span data-ttu-id="5b4a7-111">請注意，如同 LINQ 查詢，查詢才可以執行結果會列舉，在上述範例中是使用 ToList 的呼叫。</span><span class="sxs-lookup"><span data-stu-id="5b4a7-111">Note that, just as for LINQ queries, the query is not executed until the results are enumerated—in the example above this is done with the call to ToList.</span></span>  

<span data-ttu-id="5b4a7-112">只要原始 SQL 查詢會寫入兩個原因，應該特別注意。</span><span class="sxs-lookup"><span data-stu-id="5b4a7-112">Care should be taken whenever raw SQL queries are written for two reasons.</span></span> <span data-ttu-id="5b4a7-113">首先，應該撰寫查詢，以確保只會傳回實體的要求的型別實際上是。</span><span class="sxs-lookup"><span data-stu-id="5b4a7-113">First, the query should be written to ensure that it only returns entities that are really of the requested type.</span></span> <span data-ttu-id="5b4a7-114">例如，使用功能，例如繼承時很容易撰寫的查詢，將會建立屬於錯誤的 CLR 類型的實體。</span><span class="sxs-lookup"><span data-stu-id="5b4a7-114">For example, when using features such as inheritance it is easy to write a query that will create entities that are of the wrong CLR type.</span></span>  

<span data-ttu-id="5b4a7-115">第二，某些類型的原始 SQL 查詢會公開潛在的安全性風險，尤其是 SQL 資料隱碼攻擊。</span><span class="sxs-lookup"><span data-stu-id="5b4a7-115">Second, some types of raw SQL query expose potential security risks, especially around SQL injection attacks.</span></span> <span data-ttu-id="5b4a7-116">請確定在查詢中使用參數，以防範這類攻擊的正確方式。</span><span class="sxs-lookup"><span data-stu-id="5b4a7-116">Make sure that you use parameters in your query in the correct way to guard against such attacks.</span></span>  

### <a name="loading-entities-from-stored-procedures"></a><span data-ttu-id="5b4a7-117">從預存程序中載入的實體</span><span class="sxs-lookup"><span data-stu-id="5b4a7-117">Loading entities from stored procedures</span></span>  

<span data-ttu-id="5b4a7-118">您可以使用 DbSet.SqlQuery 載入實體，從預存程序的結果。</span><span class="sxs-lookup"><span data-stu-id="5b4a7-118">You can use DbSet.SqlQuery to load entities from the results of a stored procedure.</span></span> <span data-ttu-id="5b4a7-119">例如，下列程式碼會呼叫 dbo。在資料庫中的 GetBlogs 程序：</span><span class="sxs-lookup"><span data-stu-id="5b4a7-119">For example, the following code calls the dbo.GetBlogs procedure in the database:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blogs = context.Blogs.SqlQuery("dbo.GetBlogs").ToList();
}
```  

<span data-ttu-id="5b4a7-120">您也可以將參數傳遞至預存程序，使用下列語法：</span><span class="sxs-lookup"><span data-stu-id="5b4a7-120">You can also pass parameters to a stored procedure using the following syntax:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blogId = 1;

    var blogs = context.Blogs.SqlQuery("dbo.GetBlogById @p0", blogId).Single();
}
```  

## <a name="writing-sql-queries-for-non-entity-types"></a><span data-ttu-id="5b4a7-121">非實體類型的撰寫 SQL 查詢</span><span class="sxs-lookup"><span data-stu-id="5b4a7-121">Writing SQL queries for non-entity types</span></span>  

<span data-ttu-id="5b4a7-122">資料庫類別使用 SqlQuery 方法可以建立 SQL 查詢傳回的任何類型，包括基本類型的執行個體。</span><span class="sxs-lookup"><span data-stu-id="5b4a7-122">A SQL query returning instances of any type, including primitive types, can be created using the SqlQuery method on the Database class.</span></span> <span data-ttu-id="5b4a7-123">例如: </span><span class="sxs-lookup"><span data-stu-id="5b4a7-123">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blogNames = context.Database.SqlQuery<string>(
                       "SELECT Name FROM dbo.Blogs").ToList();
}
```  

<span data-ttu-id="5b4a7-124">即使物件是實體類型的執行個體，在資料庫上的 SqlQuery 從傳回的結果永遠不會追蹤內容。</span><span class="sxs-lookup"><span data-stu-id="5b4a7-124">The results returned from SqlQuery on Database will never be tracked by the context even if the objects are instances of an entity type.</span></span>  

## <a name="sending-raw-commands-to-the-database"></a><span data-ttu-id="5b4a7-125">將未經處理的命令傳送至資料庫</span><span class="sxs-lookup"><span data-stu-id="5b4a7-125">Sending raw commands to the database</span></span>  

<span data-ttu-id="5b4a7-126">非查詢命令可以傳送到使用 ExecuteSqlCommand 方法，在資料庫上的資料庫。</span><span class="sxs-lookup"><span data-stu-id="5b4a7-126">Non-query commands can be sent to the database using the ExecuteSqlCommand method on Database.</span></span> <span data-ttu-id="5b4a7-127">例如: </span><span class="sxs-lookup"><span data-stu-id="5b4a7-127">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    context.Database.ExecuteSqlCommand(
        "UPDATE dbo.Blogs SET Name = 'Another Name' WHERE BlogId = 1");
}
```  

<span data-ttu-id="5b4a7-128">請注意，使用 ExecuteSqlCommand 中資料庫的資料所做的變更是不透明的內容，直到載入或從資料庫重新載入實體。</span><span class="sxs-lookup"><span data-stu-id="5b4a7-128">Note that any changes made to data in the database using ExecuteSqlCommand are opaque to the context until entities are loaded or reloaded from the database.</span></span>  

### <a name="output-parameters"></a><span data-ttu-id="5b4a7-129">輸出參數</span><span class="sxs-lookup"><span data-stu-id="5b4a7-129">Output Parameters</span></span>  

<span data-ttu-id="5b4a7-130">如果 output 參數使用，直到已完全讀取結果，將無法使用它們的值。</span><span class="sxs-lookup"><span data-stu-id="5b4a7-130">If output parameters are used, their values will not be available until the results have been read completely.</span></span> <span data-ttu-id="5b4a7-131">這是因為基礎行為的 DbDataReader，請參閱 <<c0> [ 擷取的資料使用 DataReader](http://go.microsoft.com/fwlink/?LinkID=398589)如需詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="5b4a7-131">This is due to the underlying behavior of DbDataReader, see [Retrieving Data Using a DataReader](http://go.microsoft.com/fwlink/?LinkID=398589) for more details.</span></span>  
