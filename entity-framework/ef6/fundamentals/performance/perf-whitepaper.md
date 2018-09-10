---
title: EF4、 EF5，與 EF6 的效能考量
author: divega
ms.date: 2016-10-23
ms.assetid: d6d5a465-6434-45fa-855d-5eb48c61a2ea
ms.openlocfilehash: 3ec061559f6ad7cbdce59118c13543d9993ec5a5
ms.sourcegitcommit: 0d36e8ff0892b7f034b765b15e041f375f88579a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2018
ms.locfileid: "44251306"
---
# <a name="performance-considerations-for-ef-4-5-and-6"></a><span data-ttu-id="eeb91-102">EF 4、 5 和 6 的效能考量</span><span class="sxs-lookup"><span data-stu-id="eeb91-102">Performance considerations for EF 4, 5, and 6</span></span>
<span data-ttu-id="eeb91-103">David Obando、 Eric Dettinger 和其他項目</span><span class="sxs-lookup"><span data-stu-id="eeb91-103">By David Obando, Eric Dettinger and others</span></span>

<span data-ttu-id="eeb91-104">發行日期： 4 月 2012</span><span class="sxs-lookup"><span data-stu-id="eeb91-104">Published: April 2012</span></span>

<span data-ttu-id="eeb91-105">上次更新日期： 2014 年</span><span class="sxs-lookup"><span data-stu-id="eeb91-105">Last updated: May 2014</span></span>

------------------------------------------------------------------------

## <a name="1-introduction"></a><span data-ttu-id="eeb91-106">1.簡介</span><span class="sxs-lookup"><span data-stu-id="eeb91-106">1. Introduction</span></span>

<span data-ttu-id="eeb91-107">物件關聯式對應架構都提供物件導向應用程式中的資料存取的抽象概念的便利方式。</span><span class="sxs-lookup"><span data-stu-id="eeb91-107">Object-Relational Mapping frameworks are a convenient way to provide an abstraction for data access in an object-oriented application.</span></span> <span data-ttu-id="eeb91-108">.NET 應用程式，Microsoft 建議的 O/RM 是 Entity Framework。</span><span class="sxs-lookup"><span data-stu-id="eeb91-108">For .NET applications, Microsoft's recommended O/RM is Entity Framework.</span></span> <span data-ttu-id="eeb91-109">不過，使用任何抽象效能可以成為問題。</span><span class="sxs-lookup"><span data-stu-id="eeb91-109">With any abstraction though, performance can become a concern.</span></span>

<span data-ttu-id="eeb91-110">本白皮書中寫入時使用 Entity Framework，讓開發人員了解 Entity Framework 內部演算法可能會影響效能，並提供秘訣以調查開發應用程式顯示的效能考量，改善他們使用 Entity Framework 的應用程式中的效能。</span><span class="sxs-lookup"><span data-stu-id="eeb91-110">This whitepaper was written to show the performance considerations when developing applications using Entity Framework, to give developers an idea of the Entity Framework internal algorithms that can affect performance, and to provide tips for investigation and improving performance in their applications that use Entity Framework.</span></span> <span data-ttu-id="eeb91-111">有幾個良好的主題，在效能上的已在 web 上，我們也試指向這些資源，可能的話，</span><span class="sxs-lookup"><span data-stu-id="eeb91-111">There are a number of good topics on performance already available on the web, and we've also tried pointing to these resources where possible.</span></span>

<span data-ttu-id="eeb91-112">效能是容易令人混淆。</span><span class="sxs-lookup"><span data-stu-id="eeb91-112">Performance is a tricky topic.</span></span> <span data-ttu-id="eeb91-113">本白皮書旨在做為資源協助您進行效能相關供應用程式使用 Entity Framework 的決策。</span><span class="sxs-lookup"><span data-stu-id="eeb91-113">This whitepaper is intended as a resource to help you make performance related decisions for your applications that use Entity Framework.</span></span> <span data-ttu-id="eeb91-114">我們已包含一些測試度量資訊，來示範的效能，但這些計量為絕對的指標，您會看到您的應用程式中的效能。</span><span class="sxs-lookup"><span data-stu-id="eeb91-114">We have included some test metrics to demonstrate performance, but these metrics aren't intended as absolute indicators of the performance you will see in your application.</span></span>

<span data-ttu-id="eeb91-115">實際上，這份文件會假設 Entity Framework 4 執行.NET 4.0 和 Entity Framework 5 和 6.NET 4.5 下執行。</span><span class="sxs-lookup"><span data-stu-id="eeb91-115">For practical purposes, this document assumes Entity Framework 4 is run under .NET 4.0 and Entity Framework 5 and 6 are run under .NET 4.5.</span></span> <span data-ttu-id="eeb91-116">Entity Framework 5 的效能改良的許多位於隨附於.NET 4.5 的核心元件。</span><span class="sxs-lookup"><span data-stu-id="eeb91-116">Many of the performance improvements made for Entity Framework 5 reside within the core components that ship with .NET 4.5.</span></span>

<span data-ttu-id="eeb91-117">Entity Framework 6 不足的頻外版本且不需依賴.NET 所隨附的 Entity Framework 元件。</span><span class="sxs-lookup"><span data-stu-id="eeb91-117">Entity Framework 6 is an out of band release and does not depend on the Entity Framework components that ship with .NET.</span></span> <span data-ttu-id="eeb91-118">Entity Framework 6 在.NET 4.0 和.NET 4.5 上運作，並可以提供.NET 4.0 從尚未升級，但想要在其應用程式中最新的 Entity Framework 版本的人巨量的效能優勢。</span><span class="sxs-lookup"><span data-stu-id="eeb91-118">Entity Framework 6 work on both .NET 4.0 and .NET 4.5, and can offer a big performance benefit to those who haven’t upgraded from .NET 4.0 but want the latest Entity Framework bits in their application.</span></span> <span data-ttu-id="eeb91-119">當這份文件提及 Entity Framework 6 時，它是指在撰寫本文時可用的最新版本： 版本 6.1.0。</span><span class="sxs-lookup"><span data-stu-id="eeb91-119">When this document mentions Entity Framework 6, it refers to the latest version available at the time of this writing: version 6.1.0.</span></span>

## <a name="2-cold-vs-warm-query-execution"></a><span data-ttu-id="eeb91-120">2.冷 vs。暖的查詢執行</span><span class="sxs-lookup"><span data-stu-id="eeb91-120">2. Cold vs. Warm Query Execution</span></span>

<span data-ttu-id="eeb91-121">第一次的任何查詢對給定的模型，Entity Framework 會大量載入和驗證模型，在幕後的工作。</span><span class="sxs-lookup"><span data-stu-id="eeb91-121">The very first time any query is made against a given model, the Entity Framework does a lot of work behind the scenes to load and validate the model.</span></span> <span data-ttu-id="eeb91-122">我們經常參考此第一個查詢做為 「 冷 」 的查詢。</span><span class="sxs-lookup"><span data-stu-id="eeb91-122">We frequently refer to this first query as a "cold" query.</span></span>  <span data-ttu-id="eeb91-123">針對已經載入模型進行進一步查詢稱為 「 暖 」 查詢，並更快。</span><span class="sxs-lookup"><span data-stu-id="eeb91-123">Further queries against an already loaded model are known as "warm" queries, and are much faster.</span></span>

<span data-ttu-id="eeb91-124">讓我們花時間的執行查詢，使用 Entity Framework 時的高階檢視，並查看其中 Entity Framework 6 改善項目。</span><span class="sxs-lookup"><span data-stu-id="eeb91-124">Let’s take a high-level view of where time is spent when executing a query using Entity Framework, and see where things are improving in Entity Framework 6.</span></span>

<span data-ttu-id="eeb91-125">**第一次的查詢執行-冷的查詢**</span><span class="sxs-lookup"><span data-stu-id="eeb91-125">**First Query Execution – cold query**</span></span>

| <span data-ttu-id="eeb91-126">程式碼使用者寫入</span><span class="sxs-lookup"><span data-stu-id="eeb91-126">Code User Writes</span></span>                                                                                     | <span data-ttu-id="eeb91-127">動作</span><span class="sxs-lookup"><span data-stu-id="eeb91-127">Action</span></span>                    | <span data-ttu-id="eeb91-128">EF4 效能影響</span><span class="sxs-lookup"><span data-stu-id="eeb91-128">EF4 Performance Impact</span></span>                                                                                                                                                                                                                                                                                                                                                                                                        | <span data-ttu-id="eeb91-129">EF5 效能影響</span><span class="sxs-lookup"><span data-stu-id="eeb91-129">EF5 Performance Impact</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                    | <span data-ttu-id="eeb91-130">EF6 效能影響</span><span class="sxs-lookup"><span data-stu-id="eeb91-130">EF6 Performance Impact</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
|:-----------------------------------------------------------------------------------------------------|:--------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `using(var db = new MyContext())` <br/> `{`                                                          | <span data-ttu-id="eeb91-131">內容建立</span><span class="sxs-lookup"><span data-stu-id="eeb91-131">Context creation</span></span>          | <span data-ttu-id="eeb91-132">Medium</span><span class="sxs-lookup"><span data-stu-id="eeb91-132">Medium</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                        | <span data-ttu-id="eeb91-133">Medium</span><span class="sxs-lookup"><span data-stu-id="eeb91-133">Medium</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | <span data-ttu-id="eeb91-134">低</span><span class="sxs-lookup"><span data-stu-id="eeb91-134">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `  var q1 = ` <br/> `    from c in db.Customers` <br/> `    where c.Id == id1` <br/> `    select c;` | <span data-ttu-id="eeb91-135">查詢運算式建立</span><span class="sxs-lookup"><span data-stu-id="eeb91-135">Query expression creation</span></span> | <span data-ttu-id="eeb91-136">低</span><span class="sxs-lookup"><span data-stu-id="eeb91-136">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                           | <span data-ttu-id="eeb91-137">低</span><span class="sxs-lookup"><span data-stu-id="eeb91-137">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="eeb91-138">低</span><span class="sxs-lookup"><span data-stu-id="eeb91-138">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `  var c1 = q1.First();`                                                                             | <span data-ttu-id="eeb91-139">LINQ 查詢執行</span><span class="sxs-lookup"><span data-stu-id="eeb91-139">LINQ query execution</span></span>      | <span data-ttu-id="eeb91-140">中繼資料載入： 高但快取</span><span class="sxs-lookup"><span data-stu-id="eeb91-140">- Metadata loading: High but cached</span></span> <br/> <span data-ttu-id="eeb91-141">-檢視產生： 可能很高，但快取</span><span class="sxs-lookup"><span data-stu-id="eeb91-141">- View generation: Potentially very high but cached</span></span> <br/> <span data-ttu-id="eeb91-142">參數的評估： 中型</span><span class="sxs-lookup"><span data-stu-id="eeb91-142">- Parameter evaluation: Medium</span></span> <br/> <span data-ttu-id="eeb91-143">-Query 轉譯： 中型</span><span class="sxs-lookup"><span data-stu-id="eeb91-143">- Query translation: Medium</span></span> <br/> <span data-ttu-id="eeb91-144">-具體化程式都會產生： 中度，但快取</span><span class="sxs-lookup"><span data-stu-id="eeb91-144">- Materializer generation: Medium but cached</span></span> <br/> <span data-ttu-id="eeb91-145">-資料庫執行查詢： 可能會很高</span><span class="sxs-lookup"><span data-stu-id="eeb91-145">- Database query execution: Potentially high</span></span> <br/> <span data-ttu-id="eeb91-146">+ C</span><span class="sxs-lookup"><span data-stu-id="eeb91-146">+ Connection.Open</span></span> <br/> <span data-ttu-id="eeb91-147">+ Command.ExecuteReader</span><span class="sxs-lookup"><span data-stu-id="eeb91-147">+ Command.ExecuteReader</span></span> <br/> <span data-ttu-id="eeb91-148">+ DataReader.Read</span><span class="sxs-lookup"><span data-stu-id="eeb91-148">+ DataReader.Read</span></span> <br/> <span data-ttu-id="eeb91-149">物件具體化： 中型</span><span class="sxs-lookup"><span data-stu-id="eeb91-149">Object materialization: Medium</span></span> <br/> <span data-ttu-id="eeb91-150">身分識別查閱： 中型</span><span class="sxs-lookup"><span data-stu-id="eeb91-150">- Identity lookup: Medium</span></span> | <span data-ttu-id="eeb91-151">中繼資料載入： 高但快取</span><span class="sxs-lookup"><span data-stu-id="eeb91-151">- Metadata loading: High but cached</span></span> <br/> <span data-ttu-id="eeb91-152">-檢視產生： 可能很高，但快取</span><span class="sxs-lookup"><span data-stu-id="eeb91-152">- View generation: Potentially very high but cached</span></span> <br/> <span data-ttu-id="eeb91-153">參數的評估： 低</span><span class="sxs-lookup"><span data-stu-id="eeb91-153">- Parameter evaluation: Low</span></span> <br/> <span data-ttu-id="eeb91-154">-Query 轉譯： 中度，但快取</span><span class="sxs-lookup"><span data-stu-id="eeb91-154">- Query translation: Medium but cached</span></span> <br/> <span data-ttu-id="eeb91-155">-具體化程式都會產生： 中度，但快取</span><span class="sxs-lookup"><span data-stu-id="eeb91-155">- Materializer generation: Medium but cached</span></span> <br/> <span data-ttu-id="eeb91-156">-資料庫執行查詢： 可能會很高 （較佳的查詢，在某些情況下）</span><span class="sxs-lookup"><span data-stu-id="eeb91-156">- Database query execution: Potentially high (Better queries in some situations)</span></span> <br/> <span data-ttu-id="eeb91-157">+ C</span><span class="sxs-lookup"><span data-stu-id="eeb91-157">+ Connection.Open</span></span> <br/> <span data-ttu-id="eeb91-158">+ Command.ExecuteReader</span><span class="sxs-lookup"><span data-stu-id="eeb91-158">+ Command.ExecuteReader</span></span> <br/> <span data-ttu-id="eeb91-159">+ DataReader.Read</span><span class="sxs-lookup"><span data-stu-id="eeb91-159">+ DataReader.Read</span></span> <br/> <span data-ttu-id="eeb91-160">物件具體化： 中型</span><span class="sxs-lookup"><span data-stu-id="eeb91-160">Object materialization: Medium</span></span> <br/> <span data-ttu-id="eeb91-161">身分識別查閱： 中型</span><span class="sxs-lookup"><span data-stu-id="eeb91-161">- Identity lookup: Medium</span></span> | <span data-ttu-id="eeb91-162">中繼資料載入： 高但快取</span><span class="sxs-lookup"><span data-stu-id="eeb91-162">- Metadata loading: High but cached</span></span> <br/> <span data-ttu-id="eeb91-163">-檢視產生： 中度，但快取</span><span class="sxs-lookup"><span data-stu-id="eeb91-163">- View generation: Medium but cached</span></span> <br/> <span data-ttu-id="eeb91-164">參數的評估： 低</span><span class="sxs-lookup"><span data-stu-id="eeb91-164">- Parameter evaluation: Low</span></span> <br/> <span data-ttu-id="eeb91-165">-Query 轉譯： 中度，但快取</span><span class="sxs-lookup"><span data-stu-id="eeb91-165">- Query translation: Medium but cached</span></span> <br/> <span data-ttu-id="eeb91-166">-具體化程式都會產生： 中度，但快取</span><span class="sxs-lookup"><span data-stu-id="eeb91-166">- Materializer generation: Medium but cached</span></span> <br/> <span data-ttu-id="eeb91-167">-資料庫執行查詢： 可能會很高 （較佳的查詢，在某些情況下）</span><span class="sxs-lookup"><span data-stu-id="eeb91-167">- Database query execution: Potentially high (Better queries in some situations)</span></span> <br/> <span data-ttu-id="eeb91-168">+ C</span><span class="sxs-lookup"><span data-stu-id="eeb91-168">+ Connection.Open</span></span> <br/> <span data-ttu-id="eeb91-169">+ Command.ExecuteReader</span><span class="sxs-lookup"><span data-stu-id="eeb91-169">+ Command.ExecuteReader</span></span> <br/> <span data-ttu-id="eeb91-170">+ DataReader.Read</span><span class="sxs-lookup"><span data-stu-id="eeb91-170">+ DataReader.Read</span></span> <br/> <span data-ttu-id="eeb91-171">物件具體化： 中度 （更快速比 EF5）</span><span class="sxs-lookup"><span data-stu-id="eeb91-171">Object materialization: Medium (Faster than EF5)</span></span> <br/> <span data-ttu-id="eeb91-172">身分識別查閱： 中型</span><span class="sxs-lookup"><span data-stu-id="eeb91-172">- Identity lookup: Medium</span></span> |
| `}`                                                                                                  | <span data-ttu-id="eeb91-173">Connection.Close</span><span class="sxs-lookup"><span data-stu-id="eeb91-173">Connection.Close</span></span>          | <span data-ttu-id="eeb91-174">低</span><span class="sxs-lookup"><span data-stu-id="eeb91-174">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                           | <span data-ttu-id="eeb91-175">低</span><span class="sxs-lookup"><span data-stu-id="eeb91-175">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="eeb91-176">低</span><span class="sxs-lookup"><span data-stu-id="eeb91-176">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |


<span data-ttu-id="eeb91-177">**第二個查詢執行-暖查詢**</span><span class="sxs-lookup"><span data-stu-id="eeb91-177">**Second Query Execution – warm query**</span></span>

| <span data-ttu-id="eeb91-178">程式碼使用者寫入</span><span class="sxs-lookup"><span data-stu-id="eeb91-178">Code User Writes</span></span>                                                                                     | <span data-ttu-id="eeb91-179">動作</span><span class="sxs-lookup"><span data-stu-id="eeb91-179">Action</span></span>                    | <span data-ttu-id="eeb91-180">EF4 效能影響</span><span class="sxs-lookup"><span data-stu-id="eeb91-180">EF4 Performance Impact</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | <span data-ttu-id="eeb91-181">EF5 效能影響</span><span class="sxs-lookup"><span data-stu-id="eeb91-181">EF5 Performance Impact</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | <span data-ttu-id="eeb91-182">EF6 效能影響</span><span class="sxs-lookup"><span data-stu-id="eeb91-182">EF6 Performance Impact</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
|:-----------------------------------------------------------------------------------------------------|:--------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `using(var db = new MyContext())` <br/> `{`                                                          | <span data-ttu-id="eeb91-183">內容建立</span><span class="sxs-lookup"><span data-stu-id="eeb91-183">Context creation</span></span>          | <span data-ttu-id="eeb91-184">Medium</span><span class="sxs-lookup"><span data-stu-id="eeb91-184">Medium</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | <span data-ttu-id="eeb91-185">Medium</span><span class="sxs-lookup"><span data-stu-id="eeb91-185">Medium</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | <span data-ttu-id="eeb91-186">低</span><span class="sxs-lookup"><span data-stu-id="eeb91-186">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `  var q1 = ` <br/> `    from c in db.Customers` <br/> `    where c.Id == id1` <br/> `    select c;` | <span data-ttu-id="eeb91-187">查詢運算式建立</span><span class="sxs-lookup"><span data-stu-id="eeb91-187">Query expression creation</span></span> | <span data-ttu-id="eeb91-188">低</span><span class="sxs-lookup"><span data-stu-id="eeb91-188">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | <span data-ttu-id="eeb91-189">低</span><span class="sxs-lookup"><span data-stu-id="eeb91-189">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | <span data-ttu-id="eeb91-190">低</span><span class="sxs-lookup"><span data-stu-id="eeb91-190">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `  var c1 = q1.First();`                                                                             | <span data-ttu-id="eeb91-191">LINQ 查詢執行</span><span class="sxs-lookup"><span data-stu-id="eeb91-191">LINQ query execution</span></span>      | <span data-ttu-id="eeb91-192">中繼資料~~載入~~查閱：~~高但快取~~低</span><span class="sxs-lookup"><span data-stu-id="eeb91-192">- Metadata ~~loading~~ lookup: ~~High but cached~~ Low</span></span> <br/> <span data-ttu-id="eeb91-193">-檢視~~代~~查閱：~~可能很高，但快取~~低</span><span class="sxs-lookup"><span data-stu-id="eeb91-193">- View ~~generation~~ lookup: ~~Potentially very high but cached~~ Low</span></span> <br/> <span data-ttu-id="eeb91-194">參數的評估： 中型</span><span class="sxs-lookup"><span data-stu-id="eeb91-194">- Parameter evaluation: Medium</span></span> <br/> <span data-ttu-id="eeb91-195">-Query~~翻譯~~查閱： 中型</span><span class="sxs-lookup"><span data-stu-id="eeb91-195">- Query ~~translation~~ lookup: Medium</span></span> <br/> <span data-ttu-id="eeb91-196">-具體化程式都會~~代~~查閱：~~中型但快取~~低</span><span class="sxs-lookup"><span data-stu-id="eeb91-196">- Materializer ~~generation~~ lookup: ~~Medium but cached~~ Low</span></span> <br/> <span data-ttu-id="eeb91-197">-資料庫執行查詢： 可能會很高</span><span class="sxs-lookup"><span data-stu-id="eeb91-197">- Database query execution: Potentially high</span></span> <br/> <span data-ttu-id="eeb91-198">+ C</span><span class="sxs-lookup"><span data-stu-id="eeb91-198">+ Connection.Open</span></span> <br/> <span data-ttu-id="eeb91-199">+ Command.ExecuteReader</span><span class="sxs-lookup"><span data-stu-id="eeb91-199">+ Command.ExecuteReader</span></span> <br/> <span data-ttu-id="eeb91-200">+ DataReader.Read</span><span class="sxs-lookup"><span data-stu-id="eeb91-200">+ DataReader.Read</span></span> <br/> <span data-ttu-id="eeb91-201">物件具體化： 中型</span><span class="sxs-lookup"><span data-stu-id="eeb91-201">Object materialization: Medium</span></span> <br/> <span data-ttu-id="eeb91-202">身分識別查閱： 中型</span><span class="sxs-lookup"><span data-stu-id="eeb91-202">- Identity lookup: Medium</span></span> | <span data-ttu-id="eeb91-203">中繼資料~~載入~~查閱：~~高但快取~~低</span><span class="sxs-lookup"><span data-stu-id="eeb91-203">- Metadata ~~loading~~ lookup: ~~High but cached~~ Low</span></span> <br/> <span data-ttu-id="eeb91-204">-檢視~~代~~查閱：~~可能很高，但快取~~低</span><span class="sxs-lookup"><span data-stu-id="eeb91-204">- View ~~generation~~ lookup: ~~Potentially very high but cached~~ Low</span></span> <br/> <span data-ttu-id="eeb91-205">參數的評估： 低</span><span class="sxs-lookup"><span data-stu-id="eeb91-205">- Parameter evaluation: Low</span></span> <br/> <span data-ttu-id="eeb91-206">-Query~~翻譯~~查閱：~~中型但快取~~低</span><span class="sxs-lookup"><span data-stu-id="eeb91-206">- Query ~~translation~~ lookup: ~~Medium but cached~~ Low</span></span> <br/> <span data-ttu-id="eeb91-207">-具體化程式都會~~代~~查閱：~~中型但快取~~低</span><span class="sxs-lookup"><span data-stu-id="eeb91-207">- Materializer ~~generation~~ lookup: ~~Medium but cached~~ Low</span></span> <br/> <span data-ttu-id="eeb91-208">-資料庫執行查詢： 可能會很高 （較佳的查詢，在某些情況下）</span><span class="sxs-lookup"><span data-stu-id="eeb91-208">- Database query execution: Potentially high (Better queries in some situations)</span></span> <br/> <span data-ttu-id="eeb91-209">+ C</span><span class="sxs-lookup"><span data-stu-id="eeb91-209">+ Connection.Open</span></span> <br/> <span data-ttu-id="eeb91-210">+ Command.ExecuteReader</span><span class="sxs-lookup"><span data-stu-id="eeb91-210">+ Command.ExecuteReader</span></span> <br/> <span data-ttu-id="eeb91-211">+ DataReader.Read</span><span class="sxs-lookup"><span data-stu-id="eeb91-211">+ DataReader.Read</span></span> <br/> <span data-ttu-id="eeb91-212">物件具體化： 中型</span><span class="sxs-lookup"><span data-stu-id="eeb91-212">Object materialization: Medium</span></span> <br/> <span data-ttu-id="eeb91-213">身分識別查閱： 中型</span><span class="sxs-lookup"><span data-stu-id="eeb91-213">- Identity lookup: Medium</span></span> | <span data-ttu-id="eeb91-214">中繼資料~~載入~~查閱：~~高但快取~~低</span><span class="sxs-lookup"><span data-stu-id="eeb91-214">- Metadata ~~loading~~ lookup: ~~High but cached~~ Low</span></span> <br/> <span data-ttu-id="eeb91-215">-檢視~~代~~查閱：~~中型但快取~~低</span><span class="sxs-lookup"><span data-stu-id="eeb91-215">- View ~~generation~~ lookup: ~~Medium but cached~~ Low</span></span> <br/> <span data-ttu-id="eeb91-216">參數的評估： 低</span><span class="sxs-lookup"><span data-stu-id="eeb91-216">- Parameter evaluation: Low</span></span> <br/> <span data-ttu-id="eeb91-217">-Query~~翻譯~~查閱：~~中型但快取~~低</span><span class="sxs-lookup"><span data-stu-id="eeb91-217">- Query ~~translation~~ lookup: ~~Medium but cached~~ Low</span></span> <br/> <span data-ttu-id="eeb91-218">-具體化程式都會~~代~~查閱：~~中型但快取~~低</span><span class="sxs-lookup"><span data-stu-id="eeb91-218">- Materializer ~~generation~~ lookup: ~~Medium but cached~~ Low</span></span> <br/> <span data-ttu-id="eeb91-219">-資料庫執行查詢： 可能會很高 （較佳的查詢，在某些情況下）</span><span class="sxs-lookup"><span data-stu-id="eeb91-219">- Database query execution: Potentially high (Better queries in some situations)</span></span> <br/> <span data-ttu-id="eeb91-220">+ C</span><span class="sxs-lookup"><span data-stu-id="eeb91-220">+ Connection.Open</span></span> <br/> <span data-ttu-id="eeb91-221">+ Command.ExecuteReader</span><span class="sxs-lookup"><span data-stu-id="eeb91-221">+ Command.ExecuteReader</span></span> <br/> <span data-ttu-id="eeb91-222">+ DataReader.Read</span><span class="sxs-lookup"><span data-stu-id="eeb91-222">+ DataReader.Read</span></span> <br/> <span data-ttu-id="eeb91-223">物件具體化： 中度 （更快速比 EF5）</span><span class="sxs-lookup"><span data-stu-id="eeb91-223">Object materialization: Medium (Faster than EF5)</span></span> <br/> <span data-ttu-id="eeb91-224">身分識別查閱： 中型</span><span class="sxs-lookup"><span data-stu-id="eeb91-224">- Identity lookup: Medium</span></span> |
| `}`                                                                                                  | <span data-ttu-id="eeb91-225">Connection.Close</span><span class="sxs-lookup"><span data-stu-id="eeb91-225">Connection.Close</span></span>          | <span data-ttu-id="eeb91-226">低</span><span class="sxs-lookup"><span data-stu-id="eeb91-226">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | <span data-ttu-id="eeb91-227">低</span><span class="sxs-lookup"><span data-stu-id="eeb91-227">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | <span data-ttu-id="eeb91-228">低</span><span class="sxs-lookup"><span data-stu-id="eeb91-228">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |


<span data-ttu-id="eeb91-229">有數種方式可減少冷和暖查詢的效能成本，並讓我們看看這些在下一節。</span><span class="sxs-lookup"><span data-stu-id="eeb91-229">There are several ways to reduce the performance cost of both cold and warm queries, and we'll take a look at these in the following section.</span></span> <span data-ttu-id="eeb91-230">具體來說，我們將探討降低模型載入原始的查詢中使用預先產生的檢視，這應有助於減輕效能難題檢視表產生期間發生的成本。</span><span class="sxs-lookup"><span data-stu-id="eeb91-230">Specifically, we'll look at reducing the cost of model loading in cold queries by using pre-generated views, which should help alleviate performance pains experienced during view generation.</span></span> <span data-ttu-id="eeb91-231">暖的查詢，我們將討論查詢計畫快取、 無追蹤查詢，以及不同的查詢執行選項。</span><span class="sxs-lookup"><span data-stu-id="eeb91-231">For warm queries, we'll cover query plan caching, no tracking queries, and different query execution options.</span></span>

### <a name="21-what-is-view-generation"></a><span data-ttu-id="eeb91-232">2.1 檢視表產生是什麼？</span><span class="sxs-lookup"><span data-stu-id="eeb91-232">2.1 What is View Generation?</span></span>

<span data-ttu-id="eeb91-233">若要了解哪些檢視產生是，我們必須先了解有哪些 「 對應檢視 」。</span><span class="sxs-lookup"><span data-stu-id="eeb91-233">In order to understand what view generation is, we must first understand what “Mapping Views” are.</span></span> <span data-ttu-id="eeb91-234">對應檢視是可執行檔的每個實體集和關聯的對應中指定的轉換。</span><span class="sxs-lookup"><span data-stu-id="eeb91-234">Mapping Views are executable representations of the transformations specified in the mapping for each entity set and association.</span></span> <span data-ttu-id="eeb91-235">就內部而言，這些對應檢視需要 CQTs （標準查詢樹狀目錄） 的形狀。</span><span class="sxs-lookup"><span data-stu-id="eeb91-235">Internally, these mapping views take the shape of CQTs (canonical query trees).</span></span> <span data-ttu-id="eeb91-236">有兩種類型的對應檢視：</span><span class="sxs-lookup"><span data-stu-id="eeb91-236">There are two types of mapping views:</span></span>

-   <span data-ttu-id="eeb91-237">查詢檢視： 這些代表，從資料庫結構描述移至概念模型所需的轉換。</span><span class="sxs-lookup"><span data-stu-id="eeb91-237">Query views: these represent the transformation necessary to go from the database schema to the conceptual model.</span></span>
-   <span data-ttu-id="eeb91-238">更新檢視： 這些代表前往從概念模型的資料庫結構描述所需的轉換。</span><span class="sxs-lookup"><span data-stu-id="eeb91-238">Update views: these represent the transformation necessary to go from the conceptual model to the database schema.</span></span>

<span data-ttu-id="eeb91-239">請記住，概念模型可能會與不同的資料庫結構描述，以各種方式。</span><span class="sxs-lookup"><span data-stu-id="eeb91-239">Keep in mind that the conceptual model might differ from the database schema in various ways.</span></span> <span data-ttu-id="eeb91-240">例如，一個單一的資料表可能會用於儲存兩個不同的實體類型的資料。</span><span class="sxs-lookup"><span data-stu-id="eeb91-240">For example, one single table might be used to store the data for two different entity types.</span></span> <span data-ttu-id="eeb91-241">繼承和非一般的對應中扮演角色的對應檢視的複雜度。</span><span class="sxs-lookup"><span data-stu-id="eeb91-241">Inheritance and non-trivial mappings play a role in the complexity of the mapping views.</span></span>

<span data-ttu-id="eeb91-242">計算這些檢視對應的規格為基礎的程序是我們呼叫檢視表產生。</span><span class="sxs-lookup"><span data-stu-id="eeb91-242">The process of computing these views based on the specification of the mapping is what we call view generation.</span></span> <span data-ttu-id="eeb91-243">檢視表產生是進行以動態方式載入模型時，或在建置階段，使用 「 預先產生的檢視 」;後者會序列化為 C 的 Entity SQL 陳述式的形式\#或 VB 檔案。</span><span class="sxs-lookup"><span data-stu-id="eeb91-243">View generation can either take place dynamically when a model is loaded, or at build time, by using "pre-generated views"; the latter are serialized in the form of Entity SQL statements to a C\# or VB file.</span></span>

<span data-ttu-id="eeb91-244">當產生檢視時，也會驗證它們。</span><span class="sxs-lookup"><span data-stu-id="eeb91-244">When views are generated, they are also validated.</span></span> <span data-ttu-id="eeb91-245">從效能觀點來看，大部分的檢視表產生會是成本的實際驗證的檢視以確保實體之間的連線有意義，並具有正確的基數為所有支援的作業。</span><span class="sxs-lookup"><span data-stu-id="eeb91-245">From a performance standpoint, the vast majority of the cost of view generation is actually the validation of the views which ensures that the connections between the entities make sense and have the correct cardinality for all the supported operations.</span></span>

<span data-ttu-id="eeb91-246">實體集合的查詢執行時，查詢會與對應的查詢檢視結合，此組合的結果執行計劃編譯器建立的備份存放區可以了解查詢的表示法。</span><span class="sxs-lookup"><span data-stu-id="eeb91-246">When a query over an entity set is executed, the query is combined with the corresponding query view, and the result of this composition is run through the plan compiler to create the representation of the query that the backing store can understand.</span></span> <span data-ttu-id="eeb91-247">SQL Server 的這個編譯的最終結果會是 T-SQL SELECT 陳述式。</span><span class="sxs-lookup"><span data-stu-id="eeb91-247">For SQL Server, the final result of this compilation will be a T-SQL SELECT statement.</span></span> <span data-ttu-id="eeb91-248">第一次執行更新的實體集合時，[更新] 檢視會透過類似的程序，將它轉換成目標資料庫的 DML 陳述式執行。</span><span class="sxs-lookup"><span data-stu-id="eeb91-248">The first time an update over an entity set is performed, the update view is run through a similar process to transform it into DML statements for the target database.</span></span>

### <a name="22-factors-that-affect-view-generation-performance"></a><span data-ttu-id="eeb91-249">2.2 因素會影響檢視表產生效能</span><span class="sxs-lookup"><span data-stu-id="eeb91-249">2.2 Factors that affect View Generation performance</span></span>

<span data-ttu-id="eeb91-250">檢視產生的步驟的效能不只取決於您的模型大小還會依據如何相互連結的模型是。</span><span class="sxs-lookup"><span data-stu-id="eeb91-250">The performance of view generation step not only depends on the size of your model but also on how interconnected the model is.</span></span> <span data-ttu-id="eeb91-251">如果兩個實體連線透過繼承鏈結或關聯，就可以說連線。</span><span class="sxs-lookup"><span data-stu-id="eeb91-251">If two Entities are connected via an inheritance chain or an Association, they are said to be connected.</span></span> <span data-ttu-id="eeb91-252">同樣地如果外部索引鍵透過連線兩個資料表，它們會連線。</span><span class="sxs-lookup"><span data-stu-id="eeb91-252">Similarly if two tables are connected via a foreign key, they are connected.</span></span> <span data-ttu-id="eeb91-253">當連接的實體和結構描述中的資料表數目增加時，檢視產生的成本增加。</span><span class="sxs-lookup"><span data-stu-id="eeb91-253">As the number of connected Entities and tables in your schemas increase, the view generation cost increases.</span></span>

<span data-ttu-id="eeb91-254">但我們進行一些最佳化來改善此，我們用來產生及驗證檢視的演算法是最糟的情況，指數。</span><span class="sxs-lookup"><span data-stu-id="eeb91-254">The algorithm that we use to generate and validate views is exponential in the worst case, though we do use some optimizations to improve this.</span></span> <span data-ttu-id="eeb91-255">似乎會對效能造成負面影響的最大因素如下：</span><span class="sxs-lookup"><span data-stu-id="eeb91-255">The biggest factors that seem to negatively affect performance are:</span></span>

-   <span data-ttu-id="eeb91-256">模型的大小，參考的實體數目和這些實體之間的關聯的量。</span><span class="sxs-lookup"><span data-stu-id="eeb91-256">Model size, referring to the number of entities and the amount of associations between these entities.</span></span>
-   <span data-ttu-id="eeb91-257">模型的複雜性，尤其是牽涉到大量類型的繼承。</span><span class="sxs-lookup"><span data-stu-id="eeb91-257">Model complexity, specifically inheritance involving a large number of types.</span></span>
-   <span data-ttu-id="eeb91-258">使用獨立關聯，而不外部索引鍵關聯。</span><span class="sxs-lookup"><span data-stu-id="eeb91-258">Using Independent Associations, instead of Foreign Key Associations.</span></span>

<span data-ttu-id="eeb91-259">小型的簡單模型的成本可能夠小，不需要考慮使用預先產生的檢視。</span><span class="sxs-lookup"><span data-stu-id="eeb91-259">For small, simple models the cost may be small enough to not bother using pre-generated views.</span></span> <span data-ttu-id="eeb91-260">當模型大小和複雜度增加，有幾個選項可用來降低成本檢視產生和驗證。</span><span class="sxs-lookup"><span data-stu-id="eeb91-260">As model size and complexity increase, there are several options available to reduce the cost of view generation and validation.</span></span>

### <a name="23-using-pre-generated-views-to-decrease-model-load-time"></a><span data-ttu-id="eeb91-261">2.3 使用 Pre-Generated 檢視，以減少模型載入時間</span><span class="sxs-lookup"><span data-stu-id="eeb91-261">2.3 Using Pre-Generated Views to decrease model load time</span></span>

#### <a name="231-pre-generated-views-using-the-entity-framework-power-tools"></a><span data-ttu-id="eeb91-262">2.3.1 使用 Entity Framework Power Tools 預先產生檢視</span><span class="sxs-lookup"><span data-stu-id="eeb91-262">2.3.1 Pre-Generated views using the Entity Framework Power Tools</span></span>

<span data-ttu-id="eeb91-263">您也可以考慮使用 Entity Framework Power Tools 產生 EDMX 和 Code First 模型的檢視模型類別檔案上按一下滑鼠右鍵，並使用 Entity Framework 功能表，選取 「 產生檢視 」。</span><span class="sxs-lookup"><span data-stu-id="eeb91-263">You can also consider using the Entity Framework Power Tools to generate views of EDMX and Code First models by right-clicking the model class file and using the Entity Framework menu to select “Generate Views”.</span></span> <span data-ttu-id="eeb91-264">Entity Framework Power Tools 只能在 DbContext 衍生內容上運作，以及位於\< http://visualstudiogallery.msdn.microsoft.com/72a60b14-1581-4b9b-89f2-846072eff19d>。</span><span class="sxs-lookup"><span data-stu-id="eeb91-264">The Entity Framework Power Tools work only on DbContext-derived contexts and can be found at \<http://visualstudiogallery.msdn.microsoft.com/72a60b14-1581-4b9b-89f2-846072eff19d>.</span></span>

<span data-ttu-id="eeb91-265">如需如何使用 Entity Framework 6 的預先產生的檢視的詳細資訊，請造訪[Pre-Generated 對應檢視](~/ef6/fundamentals/performance/pre-generated-views.md)。</span><span class="sxs-lookup"><span data-stu-id="eeb91-265">For more information on how to use pre-generated views on Entity Framework 6 visit [Pre-Generated Mapping Views](~/ef6/fundamentals/performance/pre-generated-views.md).</span></span>

#### <a name="232-how-to-use-pre-generated-views-with-a-model-created-by-edmgen"></a><span data-ttu-id="eeb91-266">2.3.2 如何使用 EDMGen 所建立的模型中的預先產生的檢視</span><span class="sxs-lookup"><span data-stu-id="eeb91-266">2.3.2 How to use Pre-generated views with a model created by EDMGen</span></span>

<span data-ttu-id="eeb91-267">EDMGen 是公用程式隨附於.NET，並使用 Entity Framework 4 和 5，但不是能搭配 Entity Framework 6 的運作方式。</span><span class="sxs-lookup"><span data-stu-id="eeb91-267">EDMGen is a utility that ships with .NET and works with Entity Framework 4 and 5, but not with Entity Framework 6.</span></span> <span data-ttu-id="eeb91-268">EDMGen 可讓您從命令列產生的模型檔案中，物件層和檢視。</span><span class="sxs-lookup"><span data-stu-id="eeb91-268">EDMGen allows you to generate a model file, the object layer and the views from the command line.</span></span> <span data-ttu-id="eeb91-269">其中一個輸出將會在您選擇的語言，VB 或 C 中的檢視檔案\#。</span><span class="sxs-lookup"><span data-stu-id="eeb91-269">One of the outputs will be a Views file in your language of choice, VB or C\#.</span></span> <span data-ttu-id="eeb91-270">這是包含 Entity SQL 程式碼片段，每一個實體集的程式碼檔案。</span><span class="sxs-lookup"><span data-stu-id="eeb91-270">This is a code file containing Entity SQL snippets for each entity set.</span></span> <span data-ttu-id="eeb91-271">若要啟用預先產生的檢視，您只需包含檔案在您的專案。</span><span class="sxs-lookup"><span data-stu-id="eeb91-271">To enable pre-generated views, you simply include the file in your project.</span></span>

<span data-ttu-id="eeb91-272">如果您手動編輯模型的結構描述檔案，您必須重新產生檢視檔案。</span><span class="sxs-lookup"><span data-stu-id="eeb91-272">If you manually make edits to the schema files for the model, you will need to re-generate the views file.</span></span> <span data-ttu-id="eeb91-273">您可以執行使用 EDMGen **/mode:ViewGeneration**旗標。</span><span class="sxs-lookup"><span data-stu-id="eeb91-273">You can do this by running EDMGen with the **/mode:ViewGeneration** flag.</span></span>

<span data-ttu-id="eeb91-274">如需進一步的參考，請參閱[如何： Pre-Generate 檢視，以改善查詢效能](https://msdn.microsoft.com/library/bb896240.aspx)。</span><span class="sxs-lookup"><span data-stu-id="eeb91-274">For further reference, see [How to: Pre-Generate Views to Improve Query Performance](https://msdn.microsoft.com/library/bb896240.aspx).</span></span>

#### <a name="233-how-to-use-pre-generated-views-with-an-edmx-file"></a><span data-ttu-id="eeb91-275">2.3.3 如何 Pre-Generated 檢視使用 EDMX 檔案</span><span class="sxs-lookup"><span data-stu-id="eeb91-275">2.3.3 How to use Pre-Generated Views with an EDMX file</span></span>

<span data-ttu-id="eeb91-276">您也可以使用 EDMGen 來產生的 EDMX 檔案的檢視表-先前參考的 MSDN 主題描述如何新增建置前事件，若要這樣做-這是複雜但有某些情況下，而不可能。</span><span class="sxs-lookup"><span data-stu-id="eeb91-276">You can also use EDMGen to generate views for an EDMX file - the previously referenced MSDN topic describes how to add a pre-build event to do this - but this is complicated and there are some cases where it isn't possible.</span></span> <span data-ttu-id="eeb91-277">它是通常更容易使用 T4 範本產生檢視，當模型處於 edmx 檔案。</span><span class="sxs-lookup"><span data-stu-id="eeb91-277">It's generally easier to use a T4 template to generate the views when your model is in an edmx file.</span></span>

<span data-ttu-id="eeb91-278">ADO.NET 小組部落格有某篇文章說明如何使用 T4 範本產生檢視表 ( \< http://blogs.msdn.com/b/adonet/archive/2008/06/20/how-to-use-a-t4-template-for-view-generation.aspx>)。</span><span class="sxs-lookup"><span data-stu-id="eeb91-278">The ADO.NET team blog has a post that describes how to use a T4 template for view generation ( \<http://blogs.msdn.com/b/adonet/archive/2008/06/20/how-to-use-a-t4-template-for-view-generation.aspx>).</span></span> <span data-ttu-id="eeb91-279">這篇文章包含可以下載並加入至專案的範本。</span><span class="sxs-lookup"><span data-stu-id="eeb91-279">This post includes a template that can be downloaded and added to your project.</span></span> <span data-ttu-id="eeb91-280">範本是針對 Entity Framework 的第一個版本撰寫的因此它們不保證適用於 Entity Framework 的最新版本。</span><span class="sxs-lookup"><span data-stu-id="eeb91-280">The template was written for the first version of Entity Framework, so they aren’t guaranteed to work with the latest versions of Entity Framework.</span></span> <span data-ttu-id="eeb91-281">不過，您可以下載較新的一組檢視產生範本，針對 Entity Framework 4 和 5from Visual Studio 組件庫：</span><span class="sxs-lookup"><span data-stu-id="eeb91-281">However, you can download a more up-to-date set of view generation templates for Entity Framework 4 and 5from the Visual Studio Gallery:</span></span>

-   <span data-ttu-id="eeb91-282">VB.NET: \<http://visualstudiogallery.msdn.microsoft.com/118b44f2-1b91-4de2-a584-7a680418941d></span><span class="sxs-lookup"><span data-stu-id="eeb91-282">VB.NET: \<http://visualstudiogallery.msdn.microsoft.com/118b44f2-1b91-4de2-a584-7a680418941d></span></span>
-   <span data-ttu-id="eeb91-283">C\#: \<http://visualstudiogallery.msdn.microsoft.com/ae7730ce-ddab-470f-8456-1b313cd2c44d></span><span class="sxs-lookup"><span data-stu-id="eeb91-283">C\#: \<http://visualstudiogallery.msdn.microsoft.com/ae7730ce-ddab-470f-8456-1b313cd2c44d></span></span>

<span data-ttu-id="eeb91-284">如果您使用 Entity Framework 6 您可以檢視產生 T4 範本從取得 Visual Studio 組件庫，在\< http://visualstudiogallery.msdn.microsoft.com/18a7db90-6705-4d19-9dd1-0a6c23d0751f>。</span><span class="sxs-lookup"><span data-stu-id="eeb91-284">If you’re using Entity Framework 6 you can get the view generation T4 templates from the Visual Studio Gallery at \<http://visualstudiogallery.msdn.microsoft.com/18a7db90-6705-4d19-9dd1-0a6c23d0751f>.</span></span>

#### <a name="234-how-to-use-pre-generated-views-with-a-code-first-model"></a><span data-ttu-id="eeb91-285">2.3.4 如何使用 Code First 模型的 Pre-Generated 檢視</span><span class="sxs-lookup"><span data-stu-id="eeb91-285">2.3.4 How to use Pre-Generated Views with a Code First model</span></span>

<span data-ttu-id="eeb91-286">它也可使用的第一個程式碼的專案中的預先產生的檢視。</span><span class="sxs-lookup"><span data-stu-id="eeb91-286">It's also possible to use pre-generated views with a Code First project.</span></span> <span data-ttu-id="eeb91-287">Entity Framework Power Tools 都能夠產生檢視檔案的第一個程式碼專案。</span><span class="sxs-lookup"><span data-stu-id="eeb91-287">The Entity Framework Power Tools has the ability to generate a views file for your Code First project.</span></span> <span data-ttu-id="eeb91-288">您可以在 Visual Studio 組件庫中找到 Entity Framework Power Tools \< http://visualstudiogallery.msdn.microsoft.com/72a60b14-1581-4b9b-89f2-846072eff19d/>。</span><span class="sxs-lookup"><span data-stu-id="eeb91-288">You can find the Entity Framework Power Tools in the Visual Studio Gallery at \<http://visualstudiogallery.msdn.microsoft.com/72a60b14-1581-4b9b-89f2-846072eff19d/>.</span></span>

### <a name="24-reducing-the-cost-of-view-generation"></a><span data-ttu-id="eeb91-289">2.4 減少檢視產生的成本</span><span class="sxs-lookup"><span data-stu-id="eeb91-289">2.4 Reducing the cost of view generation</span></span>

<span data-ttu-id="eeb91-290">使用預先產生的檢視，會同時從模型載入 （執行階段） 的檢視表產生成本移到編譯時間中。</span><span class="sxs-lookup"><span data-stu-id="eeb91-290">Using pre-generated views moves the cost of view generation from model loading (run time) to compile time.</span></span> <span data-ttu-id="eeb91-291">雖然這可改善啟動效能，在執行階段，則會在您在開發時仍發生檢視產生的痛苦。</span><span class="sxs-lookup"><span data-stu-id="eeb91-291">While this improves startup performance at runtime, you will still experience the pain of view generation while you are developing.</span></span> <span data-ttu-id="eeb91-292">有數個其他的竅門，幫助降低成本的檢視表產生，在編譯時期和執行的階段。</span><span class="sxs-lookup"><span data-stu-id="eeb91-292">There are several additional tricks that can help reduce the cost of view generation, both at compile time and run time.</span></span>

#### <a name="241-using-foreign-key-associations-to-reduce-view-generation-cost"></a><span data-ttu-id="eeb91-293">2.4.1 使用外部索引鍵關聯，以減少檢視產生的成本</span><span class="sxs-lookup"><span data-stu-id="eeb91-293">2.4.1 Using Foreign Key Associations to reduce view generation cost</span></span>

<span data-ttu-id="eeb91-294">我們看到其中切換關聯外部索引鍵關聯將模型從獨立關聯中，大幅改善在檢視表產生所花費的時間的案例數目。</span><span class="sxs-lookup"><span data-stu-id="eeb91-294">We have seen a number of cases where switching the associations in the model from Independent Associations to Foreign Key Associations dramatically improved the time spent in view generation.</span></span>

<span data-ttu-id="eeb91-295">為了示範這項改進，我們會使用 EDMGen 產生 Navision 模型的兩個版本。</span><span class="sxs-lookup"><span data-stu-id="eeb91-295">To demonstrate this improvement, we generated two versions of the Navision model by using EDMGen.</span></span> <span data-ttu-id="eeb91-296">*注意： seeappendix Cfor Navision 模型的描述。*</span><span class="sxs-lookup"><span data-stu-id="eeb91-296">*Note: seeappendix Cfor a description of the Navision model.*</span></span> <span data-ttu-id="eeb91-297">針對此練習，因為其非常大量的實體和它們之間的關聯性的有趣 Navision 模型。</span><span class="sxs-lookup"><span data-stu-id="eeb91-297">The Navision model is interesting for this exercise due to its very large amount of entities and relationships between them.</span></span>

<span data-ttu-id="eeb91-298">使用外部索引鍵關聯所產生的極大的模型的一個版本和其他已產生使用獨立關聯。</span><span class="sxs-lookup"><span data-stu-id="eeb91-298">One version of this very large model was generated with Foreign Keys Associations and the other was generated with Independent Associations.</span></span> <span data-ttu-id="eeb91-299">此外，我們再逾時產生每個模型的檢視所需的時間。</span><span class="sxs-lookup"><span data-stu-id="eeb91-299">We then timed how long it took to generate the views for each model.</span></span> <span data-ttu-id="eeb91-300">實體 Framework5 測試會使用類別 EntityViewGenerator GenerateViews() 方法來產生檢視，而 Entity Framework 6 測試用來從類別 storagemappingitemcollection 由 GenerateViews() 方法。</span><span class="sxs-lookup"><span data-stu-id="eeb91-300">Entity Framework5 test used the GenerateViews() method from class EntityViewGenerator to generate the views, while the Entity Framework 6 test used the GenerateViews() method from class StorageMappingItemCollection.</span></span> <span data-ttu-id="eeb91-301">這由於程式碼重構 Entity Framework 6 程式碼基底中發生。</span><span class="sxs-lookup"><span data-stu-id="eeb91-301">This due to code restructuring that occurred in the Entity Framework 6 codebase.</span></span>

<span data-ttu-id="eeb91-302">使用 Entity Framework 5，外部索引鍵之模型的檢視表產生可以採用 65 分鐘實驗室機器中。</span><span class="sxs-lookup"><span data-stu-id="eeb91-302">Using Entity Framework 5, view generation for the model with Foreign Keys took 65 minutes in a lab machine.</span></span> <span data-ttu-id="eeb91-303">它的未知多久得花上產生使用獨立關聯模型的檢視。</span><span class="sxs-lookup"><span data-stu-id="eeb91-303">It's unknown how long it would have taken to generate the views for the model that used independent associations.</span></span> <span data-ttu-id="eeb91-304">我們保留超過一個月，我們的實驗室，安裝每月更新的電腦已重新開機前執行的測試。</span><span class="sxs-lookup"><span data-stu-id="eeb91-304">We left the test running for over a month before the machine was rebooted in our lab to install monthly updates.</span></span>

<span data-ttu-id="eeb91-305">使用 Entity Framework 6，外部索引鍵之模型的檢視表產生作業需要在相同的實驗室電腦 28 秒。</span><span class="sxs-lookup"><span data-stu-id="eeb91-305">Using Entity Framework 6, view generation for the model with Foreign Keys took 28 seconds in the same lab machine.</span></span> <span data-ttu-id="eeb91-306">使用獨立關聯模型的檢視表產生花費了 58 秒。</span><span class="sxs-lookup"><span data-stu-id="eeb91-306">View generation for the model that uses Independent Associations took 58 seconds.</span></span> <span data-ttu-id="eeb91-307">為 Entity Framework 6 對其檢視產生程式碼的改善表示許多專案，不需要預先產生的檢視，以取得更快速的啟動時間。</span><span class="sxs-lookup"><span data-stu-id="eeb91-307">The improvements done to Entity Framework 6 on its view generation code mean that many projects won’t need pre-generated views to obtain faster startup times.</span></span>

<span data-ttu-id="eeb91-308">務必預先產生的 Entity Framework 4 和 5 中的檢視可以使用 EDMGen 或 Entity Framework Power Tools 的備註。</span><span class="sxs-lookup"><span data-stu-id="eeb91-308">It’s important to remark that pre-generating views in Entity Framework 4 and 5 can be done with EDMGen or the Entity Framework Power Tools.</span></span> <span data-ttu-id="eeb91-309">Entity Framework 6 檢視產生可透過 Entity Framework Power Tools 或以程式設計方式中所述[Pre-Generated 對應檢視](~/ef6/fundamentals/performance/pre-generated-views.md)。</span><span class="sxs-lookup"><span data-stu-id="eeb91-309">For Entity Framework 6 view generation can be done via the Entity Framework Power Tools or programmatically as described in [Pre-Generated Mapping Views](~/ef6/fundamentals/performance/pre-generated-views.md).</span></span>

##### <a name="2411-how-to-use-foreign-keys-instead-of-independent-associations"></a><span data-ttu-id="eeb91-310">2.4.1.1、azure 如何使用外部索引鍵，而不是獨立關聯</span><span class="sxs-lookup"><span data-stu-id="eeb91-310">2.4.1.1 How to use Foreign Keys instead of Independent Associations</span></span>

<span data-ttu-id="eeb91-311">根據預設，當使用 EDMGen 或實體設計工具在 Visual Studio 中，取得 Fk，它只需要 Fk 和 IAs 之間切換單一核取方塊或命令列旗標。</span><span class="sxs-lookup"><span data-stu-id="eeb91-311">When using EDMGen or the Entity Designer in Visual Studio, you get FKs by default, and it only takes a single checkbox or command line flag to switch between FKs and IAs.</span></span>

<span data-ttu-id="eeb91-312">如果您有大型的 Code First 模型時，使用獨立關聯將會有檢視表產生相同的效果。</span><span class="sxs-lookup"><span data-stu-id="eeb91-312">If you have a large Code First model, using Independent Associations will have the same effect on view generation.</span></span> <span data-ttu-id="eeb91-313">雖然有些開發人員會認為這到處其物件模型，您可以避免這種影響加上您相依的物件類別的外部索引鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="eeb91-313">You can avoid this impact by including Foreign Key properties on the classes for your dependent objects, though some developers will consider this to be polluting their object model.</span></span> <span data-ttu-id="eeb91-314">您可以找到更多有關這個主題進行\< http://blog.oneunicorn.com/2011/12/11/whats-the-deal-with-mapping-foreign-keys-using-the-entity-framework/>。</span><span class="sxs-lookup"><span data-stu-id="eeb91-314">You can find more information on this subject in \<http://blog.oneunicorn.com/2011/12/11/whats-the-deal-with-mapping-foreign-keys-using-the-entity-framework/>.</span></span>

| <span data-ttu-id="eeb91-315">使用時</span><span class="sxs-lookup"><span data-stu-id="eeb91-315">When using</span></span>      | <span data-ttu-id="eeb91-316">請執行</span><span class="sxs-lookup"><span data-stu-id="eeb91-316">Do this</span></span>                                                                                                                                                                                                                                                                                                                              |
|:----------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="eeb91-317">Entity Designer</span><span class="sxs-lookup"><span data-stu-id="eeb91-317">Entity Designer</span></span> | <span data-ttu-id="eeb91-318">新增兩個實體之間的關聯之後, 請確定您已參考條件約束。</span><span class="sxs-lookup"><span data-stu-id="eeb91-318">After adding an association between two entities, make sure you have a referential constraint.</span></span> <span data-ttu-id="eeb91-319">參考條件約束會告知 Entity Framework 使用外部索引鍵，而不是獨立的關聯。</span><span class="sxs-lookup"><span data-stu-id="eeb91-319">Referential constraints tell Entity Framework to use Foreign Keys instead of Independent Associations.</span></span> <span data-ttu-id="eeb91-320">如需其他詳細資料請造訪\< http://blogs.msdn.com/b/efdesign/archive/2009/03/16/foreign-keys-in-the-entity-framework.aspx>。</span><span class="sxs-lookup"><span data-stu-id="eeb91-320">For additional details visit \<http://blogs.msdn.com/b/efdesign/archive/2009/03/16/foreign-keys-in-the-entity-framework.aspx>.</span></span> |
| <span data-ttu-id="eeb91-321">EDMGen</span><span class="sxs-lookup"><span data-stu-id="eeb91-321">EDMGen</span></span>          | <span data-ttu-id="eeb91-322">當使用 EDMGen 從資料庫產生您的檔案，將遵守您的外部索引鍵，並加入至這類模型。</span><span class="sxs-lookup"><span data-stu-id="eeb91-322">When using EDMGen to generate your files from the database, your Foreign Keys will be respected and added to the model as such.</span></span> <span data-ttu-id="eeb91-323">如需 EDMGen 所公開的不同選項的詳細資訊請造訪[ http://msdn.microsoft.com/library/bb387165.aspx ](https://msdn.microsoft.com/library/bb387165.aspx)。</span><span class="sxs-lookup"><span data-stu-id="eeb91-323">For more information on the different options exposed by EDMGen visit [http://msdn.microsoft.com/library/bb387165.aspx](https://msdn.microsoft.com/library/bb387165.aspx).</span></span>                           |
| <span data-ttu-id="eeb91-324">Code First</span><span class="sxs-lookup"><span data-stu-id="eeb91-324">Code First</span></span>      | <span data-ttu-id="eeb91-325">請參閱 「 關聯性慣例 」 一節[程式碼的第一個慣例](~/ef6/modeling/code-first/conventions/built-in.md)主題以取得有關如何使用 Code First 時，包含相依物件上的外部索引鍵屬性的資訊。</span><span class="sxs-lookup"><span data-stu-id="eeb91-325">See the "Relationship Convention" section of the [Code First Conventions](~/ef6/modeling/code-first/conventions/built-in.md) topic for information on how to include foreign key properties on dependent objects when using Code First.</span></span>                                                                                              |

#### <a name="242-moving-your-model-to-a-separate-assembly"></a><span data-ttu-id="eeb91-326">2.4.2 將模型移至不同的組件</span><span class="sxs-lookup"><span data-stu-id="eeb91-326">2.4.2 Moving your model to a separate assembly</span></span>

<span data-ttu-id="eeb91-327">當您的模型直接包含在您的應用程式專案，並產生透過建置前事件或 T4 範本檢視時，檢視產生和驗證就會進行時就會重建專案，即使模型沒有變更。</span><span class="sxs-lookup"><span data-stu-id="eeb91-327">When your model is included directly in your application's project and you generate views through a pre-build event or a T4 template, view generation and validation will take place whenever the project is rebuilt, even if the model wasn't changed.</span></span> <span data-ttu-id="eeb91-328">如果您將模型移至不同的組件，並從您的應用程式專案參考它，您可以您的應用程式進行其他變更，而無須重新建置專案包含模型項目。</span><span class="sxs-lookup"><span data-stu-id="eeb91-328">If you move the model to a separate assembly and reference it from your application's project, you can make other changes to your application without needing to rebuild the project containing the model.</span></span>

<span data-ttu-id="eeb91-329">*注意：* 時移動您的模型，來分隔組件，請記得將此模型的連接字串複製到用戶端專案的應用程式組態檔。</span><span class="sxs-lookup"><span data-stu-id="eeb91-329">*Note:*  when moving your model to separate assemblies remember to copy the connection strings for the model into the application configuration file of the client project.</span></span>

#### <a name="243-disable-validation-of-an-edmx-based-model"></a><span data-ttu-id="eeb91-330">2.4.3 停用的 edmx 式模型的驗證</span><span class="sxs-lookup"><span data-stu-id="eeb91-330">2.4.3 Disable validation of an edmx-based model</span></span>

<span data-ttu-id="eeb91-331">EDMX 模型會在編譯時期驗證，即使模型不會變更。</span><span class="sxs-lookup"><span data-stu-id="eeb91-331">EDMX models are validated at compile time, even if the model is unchanged.</span></span> <span data-ttu-id="eeb91-332">如果您的模型已經過驗證，您可以隱藏在編譯時期的驗證屬性 視窗中將驗證上建置 屬性設定為 false。</span><span class="sxs-lookup"><span data-stu-id="eeb91-332">If your model has already been validated, you can suppress validation at compile time by setting the "Validate on Build" property to false in the properties window.</span></span> <span data-ttu-id="eeb91-333">當您變更您的對應或模型時，您可以暫時重新啟用驗證，以確認您的變更。</span><span class="sxs-lookup"><span data-stu-id="eeb91-333">When you change your mapping or model, you can temporarily re-enable validation to verify your changes.</span></span>

<span data-ttu-id="eeb91-334">請注意，效能已改善 Entity Framework 設計工具的 Entity Framework 6，而且 「 驗證上建置 」 的成本會遠低於舊版設計工具中。</span><span class="sxs-lookup"><span data-stu-id="eeb91-334">Note that performance improvements were made to the Entity Framework Designer for Entity Framework 6, and the cost of the “Validate on Build” is much lower than in previous versions of the designer.</span></span>

## <a name="3-caching-in-the-entity-framework"></a><span data-ttu-id="eeb91-335">Entity Framework 中的 3 個快取</span><span class="sxs-lookup"><span data-stu-id="eeb91-335">3 Caching in the Entity Framework</span></span>

<span data-ttu-id="eeb91-336">Entity Framework 有快取的內建以下列形式：</span><span class="sxs-lookup"><span data-stu-id="eeb91-336">Entity Framework has the following forms of caching built-in:</span></span>

1.  <span data-ttu-id="eeb91-337">物件快取 – 內建的 ObjectContext 執行個體在 ObjectStateManager 中已使用該執行個體已擷取之物件的記憶體會追蹤。</span><span class="sxs-lookup"><span data-stu-id="eeb91-337">Object caching – the ObjectStateManager built into an ObjectContext instance keeps track in memory of the objects that have been retrieved using that instance.</span></span> <span data-ttu-id="eeb91-338">這也就是第一層快取。</span><span class="sxs-lookup"><span data-stu-id="eeb91-338">This is also known as first-level cache.</span></span>
2.  <span data-ttu-id="eeb91-339">查詢計劃快取-多次執行查詢時，重複使用產生的存放區命令。</span><span class="sxs-lookup"><span data-stu-id="eeb91-339">Query Plan Caching - reusing the generated store command when a query is executed more than once.</span></span>
3.  <span data-ttu-id="eeb91-340">中繼資料快取-不同的連線以相同的模型之間共用模型的中繼資料。</span><span class="sxs-lookup"><span data-stu-id="eeb91-340">Metadata caching - sharing the metadata for a model across different connections to the same model.</span></span>

<span data-ttu-id="eeb91-341">除了根據預設，也就是一種特殊的 ADO.NET 資料提供者稱為包裝提供者也可用來擴充 Entity Framework 從資料庫擷取的結果快取的 EF 提供的快取也稱為第二層快取。</span><span class="sxs-lookup"><span data-stu-id="eeb91-341">Besides the caches that EF provides out of the box, a special kind of ADO.NET data provider known as a wrapping provider can also be used to extend Entity Framework with a cache for the results retrieved from the database, also known as second-level caching.</span></span>

### <a name="31-object-caching"></a><span data-ttu-id="eeb91-342">3.1 物件快取</span><span class="sxs-lookup"><span data-stu-id="eeb91-342">3.1 Object Caching</span></span>

<span data-ttu-id="eeb91-343">預設查詢的結果中傳回實體時只 EF 會具體化之前, ObjectContext 會檢查具有相同索引鍵的實體是否已載入至其 ObjectStateManager。</span><span class="sxs-lookup"><span data-stu-id="eeb91-343">By default when an entity is returned in the results of a query, just before EF materializes it, the ObjectContext will check if an entity with the same key has already been loaded into its ObjectStateManager.</span></span> <span data-ttu-id="eeb91-344">如果具有相同的索引鍵的實體已存在於 EF 會包含在查詢的結果。</span><span class="sxs-lookup"><span data-stu-id="eeb91-344">If an entity with the same keys is already present EF will include it in the results of the query.</span></span> <span data-ttu-id="eeb91-345">雖然 EF 仍會發出對資料庫的查詢，此行為可以略過大部分的具體化實體多次的成本。</span><span class="sxs-lookup"><span data-stu-id="eeb91-345">Although EF will still issue the query against the database, this behavior can bypass much of the cost of materializing the entity multiple times.</span></span>

#### <a name="311-getting-entities-from-the-object-cache-using-dbcontext-find"></a><span data-ttu-id="eeb91-346">3.1.1 從使用 DbContext 尋找的物件快取取得實體</span><span class="sxs-lookup"><span data-stu-id="eeb91-346">3.1.1 Getting entities from the object cache using DbContext Find</span></span>

<span data-ttu-id="eeb91-347">不同於一般的查詢，DbSet (第一次在 EF 4.1 中所包含的 Api) 中的 Find 方法會搜尋在記憶體中執行之前甚至發出對資料庫的查詢。</span><span class="sxs-lookup"><span data-stu-id="eeb91-347">Unlike a regular query, the Find method in DbSet (APIs included for the first time in EF 4.1) will perform a search in memory before even issuing the query against the database.</span></span> <span data-ttu-id="eeb91-348">請務必請注意，兩個不同的 ObjectContext 執行個體將會有兩個不同 ObjectStateManager 執行個體，這表示它們有不同的物件快取。</span><span class="sxs-lookup"><span data-stu-id="eeb91-348">It’s important to note that two different ObjectContext instances will have two different ObjectStateManager instances, meaning that they have separate object caches.</span></span>

<span data-ttu-id="eeb91-349">尋找使用以嘗試尋找內容追蹤之實體的主索引鍵值。</span><span class="sxs-lookup"><span data-stu-id="eeb91-349">Find uses the primary key value to attempt to find an entity tracked by the context.</span></span> <span data-ttu-id="eeb91-350">如果實體不在內容中會接著執行並針對資料庫中，評估查詢，如果實體找不到內容中，或在資料庫中，會傳回 null。</span><span class="sxs-lookup"><span data-stu-id="eeb91-350">If the entity is not in the context then a query will be executed and evaluated against the database, and null is returned if the entity is not found in the context or in the database.</span></span> <span data-ttu-id="eeb91-351">請注意，尋找也會傳回已加入至內容，但尚未儲存到資料庫的實體。</span><span class="sxs-lookup"><span data-stu-id="eeb91-351">Note that Find also returns entities that have been added to the context but have not yet been saved to the database.</span></span>

<span data-ttu-id="eeb91-352">沒有使用尋找時所要採取的效能考量。</span><span class="sxs-lookup"><span data-stu-id="eeb91-352">There is a performance consideration to be taken when using Find.</span></span> <span data-ttu-id="eeb91-353">根據預設，這個方法的引動過程將會觸發物件快取驗證，以便偵測仍在等待認可到資料庫的變更。</span><span class="sxs-lookup"><span data-stu-id="eeb91-353">Invocations to this method by default will trigger a validation of the object cache in order to detect changes that are still pending commit to the database.</span></span> <span data-ttu-id="eeb91-354">此程序可能非常昂貴，如果有非常大量的物件快取中，或加入物件快取的大型物件圖形中的物件，但它可以也停用。</span><span class="sxs-lookup"><span data-stu-id="eeb91-354">This process can be very expensive if there are a very large number of objects in the object cache or in a large object graph being added to the object cache, but it can also be disabled.</span></span> <span data-ttu-id="eeb91-355">在某些情況下，您可能會感覺透過一個級距中呼叫方法，當您停用自動偵測尋找的差異變更。</span><span class="sxs-lookup"><span data-stu-id="eeb91-355">In certain cases, you may perceive over an order of magnitude of difference in calling the Find method when you disable auto detect changes.</span></span> <span data-ttu-id="eeb91-356">尚未第二個量級被認為當物件實際上是與快取中物件具有要從資料庫擷取時。</span><span class="sxs-lookup"><span data-stu-id="eeb91-356">Yet a second order of magnitude is perceived when the object actually is in the cache versus when the object has to be retrieved from the database.</span></span> <span data-ttu-id="eeb91-357">以下是與使用一些我們 microbenchmarks，以毫秒表示，負載為 5000 實體所做的度量的範例圖表：</span><span class="sxs-lookup"><span data-stu-id="eeb91-357">Here is an example graph with measurements taken using some of our microbenchmarks, expressed in milliseconds, with a load of 5000 entities:</span></span>

<span data-ttu-id="eeb91-358">![.NET 4.5 對數刻度](~/ef6/media/net45logscale.png ".NET 4.5-對數刻度")</span><span class="sxs-lookup"><span data-stu-id="eeb91-358">![.NET 4.5 logarithmic scale](~/ef6/media/net45logscale.png ".NET 4.5 - logarithmic scale")</span></span>

<span data-ttu-id="eeb91-359">停用偵測變更的尋找範例：</span><span class="sxs-lookup"><span data-stu-id="eeb91-359">Example of Find with auto-detect changes disabled:</span></span>

``` csharp
    context.Configuration.AutoDetectChangesEnabled = false;
    var product = context.Products.Find(productId);
    context.Configuration.AutoDetectChangesEnabled = true;
    ...
```

<span data-ttu-id="eeb91-360">您必須使用 Find 方法時，請考慮為：</span><span class="sxs-lookup"><span data-stu-id="eeb91-360">What you have to consider when using the Find method is:</span></span>

1.  <span data-ttu-id="eeb91-361">物件是否不在快取中尋找的優點否定的但的語法是由索引鍵的查詢比仍簡單。</span><span class="sxs-lookup"><span data-stu-id="eeb91-361">If the object is not in the cache the benefits of Find are negated, but the syntax is still simpler than a query by key.</span></span>
2.  <span data-ttu-id="eeb91-362">如果自動偵測變更已啟用一個級距，或甚至更是根據您的模型和您的物件快取中的實體數量的複雜度，可能會增加成本的 Find 方法。</span><span class="sxs-lookup"><span data-stu-id="eeb91-362">If auto detect changes is enabled the cost of the Find method may increase by one order of magnitude, or even more depending on the complexity of your model and the amount of entities in your object cache.</span></span>

<span data-ttu-id="eeb91-363">此外，請記住，尋找只會傳回您所需的實體，而且它不會自動載入其相關聯的實體如果它們不存在於物件快取。</span><span class="sxs-lookup"><span data-stu-id="eeb91-363">Also, keep in mind that Find only returns the entity you are looking for and it does not automatically loads its associated entities if they are not already in the object cache.</span></span> <span data-ttu-id="eeb91-364">如果您需要擷取相關聯的實體，您可以使用查詢，依據索引鍵搭配積極式載入。</span><span class="sxs-lookup"><span data-stu-id="eeb91-364">If you need to retrieve associated entities, you can use a query by key with eager loading.</span></span> <span data-ttu-id="eeb91-365">如需詳細資訊，請參閱**8.1 消極式載入 vs。積極式載入**。</span><span class="sxs-lookup"><span data-stu-id="eeb91-365">For more information see **8.1 Lazy Loading vs. Eager Loading**.</span></span>

#### <a name="312-performance-issues-when-the-object-cache-has-many-entities"></a><span data-ttu-id="eeb91-366">3.1.2 效能問題，當物件快取具有許多實體</span><span class="sxs-lookup"><span data-stu-id="eeb91-366">3.1.2 Performance issues when the object cache has many entities</span></span>

<span data-ttu-id="eeb91-367">物件快取有助於增加 Entity Framework 的整體回應。</span><span class="sxs-lookup"><span data-stu-id="eeb91-367">The object cache helps to increase the overall responsiveness of Entity Framework.</span></span> <span data-ttu-id="eeb91-368">不過，當物件快取有非常大量的實體載入它可能會影響特定作業，例如新增、 移除、 尋找項目、 SaveChanges 和更多功能。</span><span class="sxs-lookup"><span data-stu-id="eeb91-368">However, when the object cache has a very large amount of entities loaded it may affect certain operations such as Add, Remove, Find, Entry, SaveChanges and more.</span></span> <span data-ttu-id="eeb91-369">特別的是，觸發呼叫 DetectChanges 作業將會造成負面影響的超大型物件快取。</span><span class="sxs-lookup"><span data-stu-id="eeb91-369">In particular, operations that trigger a call to DetectChanges will be negatively affected by very large object caches.</span></span> <span data-ttu-id="eeb91-370">以物件狀態管理員和直接由物件圖形的大小決定其效能會 DetectChanges 同步處理的物件圖形。</span><span class="sxs-lookup"><span data-stu-id="eeb91-370">DetectChanges synchronizes the object graph with the object state manager and its performance will determined directly by the size of the object graph.</span></span> <span data-ttu-id="eeb91-371">如需 DetectChanges 的詳細資訊，請參閱[追蹤 POCO 實體變更](https://msdn.microsoft.com/library/dd456848.aspx)。</span><span class="sxs-lookup"><span data-stu-id="eeb91-371">For more information about DetectChanges, see [Tracking Changes in POCO Entities](https://msdn.microsoft.com/library/dd456848.aspx).</span></span>

<span data-ttu-id="eeb91-372">當使用 Entity Framework 6，開發人員都能夠直接在 DbSet，而不是逐一查看集合，並呼叫一次每個執行個體上呼叫 AddRange 與 RemoveRange。</span><span class="sxs-lookup"><span data-stu-id="eeb91-372">When using Entity Framework 6, developers are able to call AddRange and RemoveRange directly on a DbSet, instead of iterating on a collection and calling Add once per instance.</span></span> <span data-ttu-id="eeb91-373">使用範圍方法的優點是，DetectChanges 成本只支付一次完整的實體，而不是每個加入的實體每一次。</span><span class="sxs-lookup"><span data-stu-id="eeb91-373">The advantage of using the range methods is that the cost of DetectChanges is only paid once for the entire set of entities as opposed to once per each added entity.</span></span>

### <a name="32-query-plan-caching"></a><span data-ttu-id="eeb91-374">3.2，查詢計畫快取</span><span class="sxs-lookup"><span data-stu-id="eeb91-374">3.2 Query Plan Caching</span></span>

<span data-ttu-id="eeb91-375">第一次執行查詢時，會經過內部的計劃編譯器概念的查詢轉譯為存放區命令 (例如，T-SQL 時對 SQL Server 執行所執行)。</span><span class="sxs-lookup"><span data-stu-id="eeb91-375">The first time a query is executed, it goes through the internal plan compiler to translate the conceptual query into the store command (for example, the T-SQL which is executed when run against SQL Server).</span></span>  <span data-ttu-id="eeb91-376">如果已啟用查詢計畫快取下, 一次查詢執行存放區命令會擷取直接從執行，並計劃編譯器略過的查詢計畫快取。</span><span class="sxs-lookup"><span data-stu-id="eeb91-376">If query plan caching is enabled, the next time the query is executed the store command is retrieved directly from the query plan cache for execution, bypassing the plan compiler.</span></span>

<span data-ttu-id="eeb91-377">查詢計畫快取會共用相同的 AppDomain 內的 ObjectContext 執行個體。</span><span class="sxs-lookup"><span data-stu-id="eeb91-377">The query plan cache is shared across ObjectContext instances within the same AppDomain.</span></span> <span data-ttu-id="eeb91-378">您不需要保留的 ObjectContext 執行個體，才會受益於查詢計畫快取。</span><span class="sxs-lookup"><span data-stu-id="eeb91-378">You don't need to hold onto an ObjectContext instance to benefit from query plan caching.</span></span>

#### <a name="321-some-notes-about-query-plan-caching"></a><span data-ttu-id="eeb91-379">3.2.1 某些查詢計劃快取的注意事項</span><span class="sxs-lookup"><span data-stu-id="eeb91-379">3.2.1 Some notes about Query Plan Caching</span></span>

-   <span data-ttu-id="eeb91-380">查詢計畫快取會共用所有的查詢類型： Entity SQL，LINQ to Entities，和 CompiledQuery 物件。</span><span class="sxs-lookup"><span data-stu-id="eeb91-380">The query plan cache is shared for all query types: Entity SQL, LINQ to Entities, and CompiledQuery objects.</span></span>
-   <span data-ttu-id="eeb91-381">根據預設，查詢計劃啟用快取 Entity SQL 查詢，是否執行了 EntityCommand 來透過或 ObjectQuery。</span><span class="sxs-lookup"><span data-stu-id="eeb91-381">By default, query plan caching is enabled for Entity SQL queries, whether executed through an EntityCommand or through an ObjectQuery.</span></span> <span data-ttu-id="eeb91-382">它也會啟用預設 linq to Entities 查詢中在.NET 4.5 中，Entity Framework 和 Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="eeb91-382">It is also enabled by default for LINQ to Entities queries in Entity Framework on .NET 4.5, and in Entity Framework 6</span></span>
    -   <span data-ttu-id="eeb91-383">查詢計畫快取可停用 （在 EntityCommand 或 ObjectQuery） EnablePlanCaching 屬性設定為 false。</span><span class="sxs-lookup"><span data-stu-id="eeb91-383">Query plan caching can be disabled by setting the EnablePlanCaching property (on EntityCommand or ObjectQuery) to false.</span></span> <span data-ttu-id="eeb91-384">例如: </span><span class="sxs-lookup"><span data-stu-id="eeb91-384">For example:</span></span>
``` csharp
                    var query = from customer in context.Customer
                                where customer.CustomerId == id
                                select new
                                {
                                    customer.CustomerId,
                                    customer.Name
                                };
                    ObjectQuery oQuery = query as ObjectQuery;
                    oQuery.EnablePlanCaching = false;
```
-   <span data-ttu-id="eeb91-385">參數化查詢，只要變更參數的值會仍然達到快取的查詢。</span><span class="sxs-lookup"><span data-stu-id="eeb91-385">For parameterized queries, changing the parameter's value will still hit the cached query.</span></span> <span data-ttu-id="eeb91-386">不過，變更參數的 facet （比方說，大小、 有效位數或小數位數） 會叫用快取中的不同項目。</span><span class="sxs-lookup"><span data-stu-id="eeb91-386">But changing a parameter's facets (for example, size, precision, or scale) will hit a different entry in the cache.</span></span>
-   <span data-ttu-id="eeb91-387">使用 Entity SQL，查詢字串時的索引鍵的一部分。</span><span class="sxs-lookup"><span data-stu-id="eeb91-387">When using Entity SQL, the query string is part of the key.</span></span> <span data-ttu-id="eeb91-388">所有變更查詢會導致不同的快取的項目，即使查詢的功能相同。</span><span class="sxs-lookup"><span data-stu-id="eeb91-388">Changing the query at all will result in different cache entries, even if the queries are functionally equivalent.</span></span> <span data-ttu-id="eeb91-389">這包括變更大小寫或空白字元。</span><span class="sxs-lookup"><span data-stu-id="eeb91-389">This includes changes to casing or whitespace.</span></span>
-   <span data-ttu-id="eeb91-390">使用 LINQ 時，會處理查詢時產生索引鍵的一部分。</span><span class="sxs-lookup"><span data-stu-id="eeb91-390">When using LINQ, the query is processed to generate a part of the key.</span></span> <span data-ttu-id="eeb91-391">LINQ 運算式變更將會因此產生不同的金鑰。</span><span class="sxs-lookup"><span data-stu-id="eeb91-391">Changing the LINQ expression will therefore generate a different key.</span></span>
-   <span data-ttu-id="eeb91-392">其他技術可能會限制;如需詳細資訊，請參閱 Autocompiled 查詢。</span><span class="sxs-lookup"><span data-stu-id="eeb91-392">Other technical limitations may apply; see Autocompiled Queries for more details.</span></span>

#### <a name="322------cache-eviction-algorithm"></a><span data-ttu-id="eeb91-393">3.2.2 快取收回演算法</span><span class="sxs-lookup"><span data-stu-id="eeb91-393">3.2.2      Cache eviction algorithm</span></span>

<span data-ttu-id="eeb91-394">了解內部演算法運作方式如何協助您找出何時要啟用或停用查詢計畫快取。</span><span class="sxs-lookup"><span data-stu-id="eeb91-394">Understanding how the internal algorithm works will help you figure out when to enable or disable query plan caching.</span></span> <span data-ttu-id="eeb91-395">清除演算法如下所示：</span><span class="sxs-lookup"><span data-stu-id="eeb91-395">The cleanup algorithm is as follows:</span></span>

1.  <span data-ttu-id="eeb91-396">快取包含固定數目的項目 (800) 之後, 我們會啟動計時器，定期 （一次每分鐘） 掃掠快取。</span><span class="sxs-lookup"><span data-stu-id="eeb91-396">Once the cache contains a set number of entries (800), we start a timer that periodically (once-per-minute) sweeps the cache.</span></span>
2.  <span data-ttu-id="eeb91-397">快取掃掠，期間從 LFRU （最低常見問題-最近使用過的） 上的快取移除項目為基礎。</span><span class="sxs-lookup"><span data-stu-id="eeb91-397">During cache sweeps, entries are removed from the cache on a LFRU (Least frequently – recently used) basis.</span></span> <span data-ttu-id="eeb91-398">此演算法會叫用的次數和年齡考量決定哪些項目會退出時。</span><span class="sxs-lookup"><span data-stu-id="eeb91-398">This algorithm takes both hit count and age into account when deciding which entries are ejected.</span></span>
3.  <span data-ttu-id="eeb91-399">在每個快取掃掠的結束時，快取一次包含 800 的項目。</span><span class="sxs-lookup"><span data-stu-id="eeb91-399">At the end of each cache sweep, the cache again contains 800 entries.</span></span>

<span data-ttu-id="eeb91-400">判斷要收回的項目時，會同等對待所有快取項目。</span><span class="sxs-lookup"><span data-stu-id="eeb91-400">All cache entries are treated equally when determining which entries to evict.</span></span> <span data-ttu-id="eeb91-401">這表示 CompiledQuery 的存放區命令有相同機會收回與 Entity SQL 查詢存放區命令。</span><span class="sxs-lookup"><span data-stu-id="eeb91-401">This means the store command for a CompiledQuery has the same chance of eviction as the store command for an Entity SQL query.</span></span>

<span data-ttu-id="eeb91-402">請注意，快取收回計時器已開始時快取中，有 800 的實體，但快取只會清理此計時器就會啟動後的 60 秒。</span><span class="sxs-lookup"><span data-stu-id="eeb91-402">Note that the cache eviction timer is kicked in when there are 800 entities in the cache, but the cache is only swept 60 seconds after this timer is started.</span></span> <span data-ttu-id="eeb91-403">這表示最多 60 秒拓展您的快取可能會相當龐大。</span><span class="sxs-lookup"><span data-stu-id="eeb91-403">That means that for up to 60 seconds your cache may grow to be quite large.</span></span>

#### <a name="323-------test-metrics-demonstrating-query-plan-caching-performance"></a><span data-ttu-id="eeb91-404">3.2.3 測試示範查詢計劃快取效能度量</span><span class="sxs-lookup"><span data-stu-id="eeb91-404">3.2.3       Test Metrics demonstrating query plan caching performance</span></span>

<span data-ttu-id="eeb91-405">為了示範應用程式的效能上的快取的查詢計劃的效果，我們執行測試我們用來執行對 Navision 模型的 Entity SQL 查詢的數目。</span><span class="sxs-lookup"><span data-stu-id="eeb91-405">To demonstrate the effect of query plan caching on your application's performance, we performed a test where we executed a number of Entity SQL queries against the Navision model.</span></span> <span data-ttu-id="eeb91-406">請參閱附錄 Navision 模型所執行的查詢類型的描述。</span><span class="sxs-lookup"><span data-stu-id="eeb91-406">See the appendix for a description of the Navision model and the types of queries which were executed.</span></span> <span data-ttu-id="eeb91-407">在此測試中，我們會先逐一查看查詢的清單並將它們新增至快取 （如果已啟用快取） 一次執行每一個。</span><span class="sxs-lookup"><span data-stu-id="eeb91-407">In this test, we first iterate through the list of queries and execute each one once to add them to the cache (if caching is enabled).</span></span> <span data-ttu-id="eeb91-408">這個步驟是 untimed。</span><span class="sxs-lookup"><span data-stu-id="eeb91-408">This step is untimed.</span></span> <span data-ttu-id="eeb91-409">接下來，我們進入睡眠狀態以允許快取掃掠，以進行應用程式; 超過 60 秒的主執行緒最後，我們逐一查看清單的第 2 個開始執行快取的查詢。</span><span class="sxs-lookup"><span data-stu-id="eeb91-409">Next, we sleep the main thread for over 60 seconds to allow cache sweeping to take place; finally, we iterate through the list a 2nd time to execute the cached queries.</span></span> <span data-ttu-id="eeb91-410">此外，他的 SQL Server 計畫快取中排清之前執行查詢的每個集時，讓我們取得準確的時間會反映指定的查詢計劃快取的優點。</span><span class="sxs-lookup"><span data-stu-id="eeb91-410">Additionally, he SQL Server plan cache is flushed before each set of queries is executed so that the times we obtain accurately reflect the benefit given by the query plan cache.</span></span>

##### <a name="3231-------test-results"></a><span data-ttu-id="eeb91-411">3.2.3.1 測試結果</span><span class="sxs-lookup"><span data-stu-id="eeb91-411">3.2.3.1       Test Results</span></span>

| <span data-ttu-id="eeb91-412">測試</span><span class="sxs-lookup"><span data-stu-id="eeb91-412">Test</span></span>                                                                   | <span data-ttu-id="eeb91-413">EF5 無快取</span><span class="sxs-lookup"><span data-stu-id="eeb91-413">EF5 no cache</span></span> | <span data-ttu-id="eeb91-414">EF5 快取</span><span class="sxs-lookup"><span data-stu-id="eeb91-414">EF5 cached</span></span> | <span data-ttu-id="eeb91-415">EF6 沒有快取</span><span class="sxs-lookup"><span data-stu-id="eeb91-415">EF6 no cache</span></span> | <span data-ttu-id="eeb91-416">快取的 EF6</span><span class="sxs-lookup"><span data-stu-id="eeb91-416">EF6 cached</span></span> |
|:-----------------------------------------------------------------------|:-------------|:-----------|:-------------|:-----------|
| <span data-ttu-id="eeb91-417">列舉所有 18723 查詢</span><span class="sxs-lookup"><span data-stu-id="eeb91-417">Enumerating all 18723 queries</span></span>                                          | <span data-ttu-id="eeb91-418">124</span><span class="sxs-lookup"><span data-stu-id="eeb91-418">124</span></span>          | <span data-ttu-id="eeb91-419">125.4</span><span class="sxs-lookup"><span data-stu-id="eeb91-419">125.4</span></span>      | <span data-ttu-id="eeb91-420">124.3</span><span class="sxs-lookup"><span data-stu-id="eeb91-420">124.3</span></span>        | <span data-ttu-id="eeb91-421">125.3</span><span class="sxs-lookup"><span data-stu-id="eeb91-421">125.3</span></span>      |
| <span data-ttu-id="eeb91-422">避免掃掠 （只是先 800 查詢，不論複雜性）。</span><span class="sxs-lookup"><span data-stu-id="eeb91-422">Avoiding sweep (just the first 800 queries, regardless of complexity)</span></span>  | <span data-ttu-id="eeb91-423">41.7</span><span class="sxs-lookup"><span data-stu-id="eeb91-423">41.7</span></span>         | <span data-ttu-id="eeb91-424">5.5</span><span class="sxs-lookup"><span data-stu-id="eeb91-424">5.5</span></span>        | <span data-ttu-id="eeb91-425">40.5</span><span class="sxs-lookup"><span data-stu-id="eeb91-425">40.5</span></span>         | <span data-ttu-id="eeb91-426">5.4</span><span class="sxs-lookup"><span data-stu-id="eeb91-426">5.4</span></span>        |
| <span data-ttu-id="eeb91-427">只要 AggregatingSubtotals 查詢 （178 總計-這可避免掃掠）</span><span class="sxs-lookup"><span data-stu-id="eeb91-427">Just the AggregatingSubtotals queries (178 total - which avoids sweep)</span></span> | <span data-ttu-id="eeb91-428">39.5</span><span class="sxs-lookup"><span data-stu-id="eeb91-428">39.5</span></span>         | <span data-ttu-id="eeb91-429">4.5</span><span class="sxs-lookup"><span data-stu-id="eeb91-429">4.5</span></span>        | <span data-ttu-id="eeb91-430">38.1</span><span class="sxs-lookup"><span data-stu-id="eeb91-430">38.1</span></span>         | <span data-ttu-id="eeb91-431">4.6</span><span class="sxs-lookup"><span data-stu-id="eeb91-431">4.6</span></span>        |

<span data-ttu-id="eeb91-432">*所有時間以秒為單位。*</span><span class="sxs-lookup"><span data-stu-id="eeb91-432">*All times in seconds.*</span></span>

<span data-ttu-id="eeb91-433">道德-時執行其他的相異查詢 （例如，動態建立的查詢），快取沒有幫助，並產生清除快取可以保留最多可從計畫快取從實際使用它獲益的查詢。</span><span class="sxs-lookup"><span data-stu-id="eeb91-433">Moral - when executing lots of distinct queries (for example,  dynamically created queries), caching doesn't help and the resulting flushing of the cache can keep the queries that would benefit the most from plan caching from actually using it.</span></span>

<span data-ttu-id="eeb91-434">AggregatingSubtotals 查詢是最複雜的測試使用的查詢。</span><span class="sxs-lookup"><span data-stu-id="eeb91-434">The AggregatingSubtotals queries are the most complex of the queries we tested with.</span></span> <span data-ttu-id="eeb91-435">如預期般，越複雜的查詢是，您會看到從查詢計畫快取的效益越多。</span><span class="sxs-lookup"><span data-stu-id="eeb91-435">As expected, the more complex the query is, the more benefit you will see from query plan caching.</span></span>

<span data-ttu-id="eeb91-436">由於 CompiledQuery 其實它快取的計畫的 LINQ 查詢，比較的對等的 Entity SQL 查詢與 CompiledQuery 應該有類似的結果。</span><span class="sxs-lookup"><span data-stu-id="eeb91-436">Because a CompiledQuery is really a LINQ query with its plan cached, the comparison of a CompiledQuery versus the equivalent Entity SQL query should have similar results.</span></span> <span data-ttu-id="eeb91-437">事實上，如果應用程式有許多動態的 Entity SQL 查詢，填入查詢的快取也可以有效地會 CompiledQueries 「 不良 」 時它們會從快取中排清。</span><span class="sxs-lookup"><span data-stu-id="eeb91-437">In fact, if an app has lots of dynamic Entity SQL queries, filling the cache with queries will also effectively cause CompiledQueries to “decompile” when they are flushed from the cache.</span></span> <span data-ttu-id="eeb91-438">在此案例中，停用快取上設定優先順序 CompiledQueries 動態的查詢可能會提升效能。</span><span class="sxs-lookup"><span data-stu-id="eeb91-438">In this scenario, performance may be improved by disabling caching on the dynamic queries to prioritize the CompiledQueries.</span></span> <span data-ttu-id="eeb91-439">更棒的是，當然是重寫應用程式而不是動態的查詢使用參數化的查詢。</span><span class="sxs-lookup"><span data-stu-id="eeb91-439">Better yet, of course, would be to rewrite the app to use parameterized queries instead of dynamic queries.</span></span>

### <a name="33-using-compiledquery-to-improve-performance-with-linq-queries"></a><span data-ttu-id="eeb91-440">3.3 使用 CompiledQuery 來改善使用 LINQ 查詢的效能</span><span class="sxs-lookup"><span data-stu-id="eeb91-440">3.3 Using CompiledQuery to improve performance with LINQ queries</span></span>

<span data-ttu-id="eeb91-441">我們的測試顯示，使用 CompiledQuery 帶來的優點 7 %autocompiled 透過 LINQ 查詢;這表示，您會縮短 7%的時間執行的程式碼，從 Entity Framework 堆疊;它並不表示您的應用程式將會更快 7%。</span><span class="sxs-lookup"><span data-stu-id="eeb91-441">Our tests indicate that using CompiledQuery can bring a benefit of 7% over autocompiled LINQ queries; this means that you’ll spend 7% less time executing code from the Entity Framework stack; it does not mean your application will be 7% faster.</span></span> <span data-ttu-id="eeb91-442">一般而言，撰寫和維護 EF 5.0 CompiledQuery 物件的成本可能不值得相較於優點的麻煩。</span><span class="sxs-lookup"><span data-stu-id="eeb91-442">Generally speaking, the cost of writing and maintaining CompiledQuery objects in EF 5.0 may not be worth the trouble when compared to the benefits.</span></span> <span data-ttu-id="eeb91-443">您的級距可能有所不同，因此執行這個選項，如果您的專案需要額外的推播。</span><span class="sxs-lookup"><span data-stu-id="eeb91-443">Your mileage may vary, so exercise this option if your project requires the extra push.</span></span> <span data-ttu-id="eeb91-444">請注意，CompiledQueries 只與 ObjectContext 衍生模型相容，並且與 DbContext 衍生模式不相容。</span><span class="sxs-lookup"><span data-stu-id="eeb91-444">Note that CompiledQueries are only compatible with ObjectContext-derived models, and not compatible with DbContext-derived models.</span></span>

<span data-ttu-id="eeb91-445">如需有關建立和叫用 CompiledQuery 的詳細資訊，請參閱 <<c0> [ 編譯的查詢 (LINQ to Entities)](https://msdn.microsoft.com/library/bb896297.aspx)。</span><span class="sxs-lookup"><span data-stu-id="eeb91-445">For more information on creating and invoking a CompiledQuery, see [Compiled Queries (LINQ to Entities)](https://msdn.microsoft.com/library/bb896297.aspx).</span></span>

<span data-ttu-id="eeb91-446">有兩個您必須進行時使用 CompiledQuery 時，也就是需要使用靜態的執行個體和問題具備複合性的考量。</span><span class="sxs-lookup"><span data-stu-id="eeb91-446">There are two considerations you have to take when using a CompiledQuery, namely the requirement to use static instances and the problems they have with composability.</span></span> <span data-ttu-id="eeb91-447">以下遵循深入的說明，這些兩個考量。</span><span class="sxs-lookup"><span data-stu-id="eeb91-447">Here follows an in-depth explanation of these two considerations.</span></span>

#### <a name="331-------use-static-compiledquery-instances"></a><span data-ttu-id="eeb91-448">3.3.1 使用靜態 CompiledQuery 執行個體</span><span class="sxs-lookup"><span data-stu-id="eeb91-448">3.3.1       Use static CompiledQuery instances</span></span>

<span data-ttu-id="eeb91-449">編譯的 LINQ 查詢作業非常耗時，因為我們不想這麼做，每次我們要從資料庫擷取資料。</span><span class="sxs-lookup"><span data-stu-id="eeb91-449">Since compiling a LINQ query is a time-consuming process, we don’t want to do it every time we need to fetch data from the database.</span></span> <span data-ttu-id="eeb91-450">CompiledQuery 執行個體可讓您一次編譯及執行多次，但您必須小心，並重複使用相同的 CompiledQuery 執行個體而不是不斷地編譯它每次採購。</span><span class="sxs-lookup"><span data-stu-id="eeb91-450">CompiledQuery instances allow you to compile once and run multiple times, but you have to be careful and procure to re-use the same CompiledQuery instance every time instead of compiling it over and over again.</span></span> <span data-ttu-id="eeb91-451">使用儲存 CompiledQuery 執行個體的靜態成員會變成必要的;否則，您不會看到任何好處。</span><span class="sxs-lookup"><span data-stu-id="eeb91-451">The use of static members to store the CompiledQuery instances becomes necessary; otherwise you won’t see any benefit.</span></span>

<span data-ttu-id="eeb91-452">例如，假設您的網頁有下列的方法主體，以處理顯示選取之類別目錄的產品：</span><span class="sxs-lookup"><span data-stu-id="eeb91-452">For example, suppose your page has the following method body to handle displaying the products for the selected category:</span></span>

``` csharp
    // Warning: this is the wrong way of using CompiledQuery
    using (NorthwindEntities context = new NorthwindEntities())
    {
        string selectedCategory = this.categoriesList.SelectedValue;

        var productsForCategory = CompiledQuery.Compile<NorthwindEntities, string, IQueryable<Product>>(
            (NorthwindEntities nwnd, string category) =>
                nwnd.Products.Where(p => p.Category.CategoryName == category)
        );

        this.productsGrid.DataSource = productsForCategory.Invoke(context, selectedCategory).ToList();
        this.productsGrid.DataBind();
    }

    this.productsGrid.Visible = true;
```

<span data-ttu-id="eeb91-453">在此情況下，您將建立新的 CompiledQuery 執行個體即時，每次呼叫的方法。</span><span class="sxs-lookup"><span data-stu-id="eeb91-453">In this case, you will create a new CompiledQuery instance on the fly every time the method is called.</span></span> <span data-ttu-id="eeb91-454">而不是所擷取的查詢計劃快取存放區命令看到效能優勢，CompiledQuery 會瀏覽計劃編譯器，每次建立新的執行個體。</span><span class="sxs-lookup"><span data-stu-id="eeb91-454">Instead of seeing performance benefits by retrieving the store command from the query plan cache, the CompiledQuery will go through the plan compiler every time a new instance is created.</span></span> <span data-ttu-id="eeb91-455">事實上，您將會到處查詢計劃快取與新的 CompiledQuery 項目每次呼叫的方法。</span><span class="sxs-lookup"><span data-stu-id="eeb91-455">In fact, you will be polluting your query plan cache with a new CompiledQuery entry every time the method is called.</span></span>

<span data-ttu-id="eeb91-456">相反地，您會想要建立靜態的執行個體的已編譯的查詢，以便每次呼叫方法時，會叫用相同的已編譯的查詢。</span><span class="sxs-lookup"><span data-stu-id="eeb91-456">Instead, you want to create a static instance of the compiled query, so you are invoking the same compiled query every time the method is called.</span></span> <span data-ttu-id="eeb91-457">其中一種方式，這是由物件內容的成員身分加入 CompiledQuery 執行個體。</span><span class="sxs-lookup"><span data-stu-id="eeb91-457">One way to so this is by adding the CompiledQuery instance as a member of your object context.</span></span>  <span data-ttu-id="eeb91-458">您接著可以事項的一些更乾淨藉由存取 CompiledQuery 透過 helper 方法：</span><span class="sxs-lookup"><span data-stu-id="eeb91-458">You can then make things a little cleaner by accessing the CompiledQuery through a helper method:</span></span>

``` csharp
    public partial class NorthwindEntities : ObjectContext
    {
        private static readonly Func<NorthwindEntities, string, IEnumerable<Product>> productsForCategoryCQ = CompiledQuery.Compile(
            (NorthwindEntities context, string categoryName) =>
                context.Products.Where(p => p.Category.CategoryName == categoryName)
            );

        public IEnumerable<Product> GetProductsForCategory(string categoryName)
        {
            return productsForCategoryCQ.Invoke(this, categoryName).ToList();
        }
```

<span data-ttu-id="eeb91-459">這個 helper 方法會叫用，如下所示：</span><span class="sxs-lookup"><span data-stu-id="eeb91-459">This helper method would be invoked as follows:</span></span>

``` csharp
    this.productsGrid.DataSource = context.GetProductsForCategory(selectedCategory);
```

#### <a name="332-------composing-over-a-compiledquery"></a><span data-ttu-id="eeb91-460">3.3.2 撰寫透過 CompiledQuery</span><span class="sxs-lookup"><span data-stu-id="eeb91-460">3.3.2       Composing over a CompiledQuery</span></span>

<span data-ttu-id="eeb91-461">撰寫任何 LINQ 查詢的功能非常有用。若要這樣做，您只需叫用方法之後 IQueryable 這類*skip （)* 或是*count （)*。</span><span class="sxs-lookup"><span data-stu-id="eeb91-461">The ability to compose over any LINQ query is extremely useful; to do this, you simply invoke a method after the IQueryable such as *Skip()* or *Count()*.</span></span> <span data-ttu-id="eeb91-462">不過，所以基本上會傳回新的 IQueryable 物件。</span><span class="sxs-lookup"><span data-stu-id="eeb91-462">However, doing so essentially returns a new IQueryable object.</span></span> <span data-ttu-id="eeb91-463">沒有要阻止您技術上透過 CompiledQuery 撰寫項目，這樣會導致產生新的 IQueryable 物件所需要傳遞計劃編譯器透過一次。</span><span class="sxs-lookup"><span data-stu-id="eeb91-463">While there’s nothing to stop you technically from composing over a CompiledQuery, doing so will cause the generation of a new IQueryable object that requires passing through the plan compiler again.</span></span>

<span data-ttu-id="eeb91-464">某些元件，將使用撰寫 IQueryable 的物件，以啟用進階的功能。</span><span class="sxs-lookup"><span data-stu-id="eeb91-464">Some components will make use of composed IQueryable objects to enable advanced functionality.</span></span> <span data-ttu-id="eeb91-465">例如，ASP。NET 的 GridView 可以是資料繫結至 IQueryable 物件透過 SelectMethod 屬性。</span><span class="sxs-lookup"><span data-stu-id="eeb91-465">For example, ASP.NET’s GridView can be data-bound to an IQueryable object via the SelectMethod property.</span></span> <span data-ttu-id="eeb91-466">然後 GridView 會撰寫此 IQueryable 物件，以允許排序和分頁資料模型。</span><span class="sxs-lookup"><span data-stu-id="eeb91-466">The GridView will then compose over this IQueryable object to allow sorting and paging over the data model.</span></span> <span data-ttu-id="eeb91-467">如您所見，使用 CompiledQuery gridview 不會叫用已編譯的查詢，但會產生新的 autocompiled 查詢。</span><span class="sxs-lookup"><span data-stu-id="eeb91-467">As you can see, using a CompiledQuery for the GridView would not hit the compiled query but would generate a new autocompiled query.</span></span>

<span data-ttu-id="eeb91-468">客戶諮詢小組討論此主題中其 「 潛在效能問題與編譯的 LINQ 查詢重新編譯 」 部落格文章： <http://blogs.msdn.com/b/appfabriccat/archive/2010/08/06/potential-performance-issues-with-compiled-linq-query-re-compiles.aspx>。</span><span class="sxs-lookup"><span data-stu-id="eeb91-468">The Customer Advisory Team discusses this in their "Potential Performance Issues with Compiled LINQ Query Re-Compiles" blog post: <http://blogs.msdn.com/b/appfabriccat/archive/2010/08/06/potential-performance-issues-with-compiled-linq-query-re-compiles.aspx>.</span></span>

<span data-ttu-id="eeb91-469">其中您可能會遇到這個問題的一個位置時，漸進式的篩選器新增至查詢。</span><span class="sxs-lookup"><span data-stu-id="eeb91-469">One place where you may run into this is when adding progressive filters to a query.</span></span> <span data-ttu-id="eeb91-470">例如，假設您有使用選用的篩選條件 （例如，國家/地區和 OrdersCount） 的數個下拉式清單 [客戶] 頁面。</span><span class="sxs-lookup"><span data-stu-id="eeb91-470">For example, suppose you had a Customers page with several drop-down lists for optional filters (for example, Country and OrdersCount).</span></span> <span data-ttu-id="eeb91-471">您可以撰寫 IQueryable 結果的 CompiledQuery，這些篩選器，但這樣做會導致新的查詢進行計劃編譯器，每次您執行它。</span><span class="sxs-lookup"><span data-stu-id="eeb91-471">You can compose these filters over the IQueryable results of a CompiledQuery, but doing so will result in the new query going through the plan compiler every time you execute it.</span></span>

``` csharp
    using (NorthwindEntities context = new NorthwindEntities())
    {
        IQueryable<Customer> myCustomers = context.InvokeCustomersForEmployee();

        if (this.orderCountFilterList.SelectedItem.Value != defaultFilterText)
        {
            int orderCount = int.Parse(orderCountFilterList.SelectedValue);
            myCustomers = myCustomers.Where(c => c.Orders.Count > orderCount);
        }

        if (this.countryFilterList.SelectedItem.Value != defaultFilterText)
        {
            myCustomers = myCustomers.Where(c => c.Address.Country == countryFilterList.SelectedValue);
        }

        this.customersGrid.DataSource = myCustomers;
        this.customersGrid.DataBind();
    }
```

 <span data-ttu-id="eeb91-472">若要避免此重新編譯，您可以重新撰寫 CompiledQuery，若要篩選條件納入考量：</span><span class="sxs-lookup"><span data-stu-id="eeb91-472">To avoid this re-compilation, you can rewrite the CompiledQuery to take the possible filters into account:</span></span>

``` csharp
    private static readonly Func<NorthwindEntities, int, int?, string, IQueryable<Customer>> customersForEmployeeWithFiltersCQ = CompiledQuery.Compile(
        (NorthwindEntities context, int empId, int? countFilter, string countryFilter) =>
            context.Customers.Where(c => c.Orders.Any(o => o.EmployeeID == empId))
            .Where(c => countFilter.HasValue == false || c.Orders.Count > countFilter)
            .Where(c => countryFilter == null || c.Address.Country == countryFilter)
        );
```

<span data-ttu-id="eeb91-473">等 UI 中，會叫用：</span><span class="sxs-lookup"><span data-stu-id="eeb91-473">Which would be invoked in the UI like:</span></span>

``` csharp
    using (NorthwindEntities context = new NorthwindEntities())
    {
        int? countFilter = (this.orderCountFilterList.SelectedIndex == 0) ?
            (int?)null :
            int.Parse(this.orderCountFilterList.SelectedValue);

        string countryFilter = (this.countryFilterList.SelectedIndex == 0) ?
            null :
            this.countryFilterList.SelectedValue;

        IQueryable<Customer> myCustomers = context.InvokeCustomersForEmployeeWithFilters(
                countFilter, countryFilter);

        this.customersGrid.DataSource = myCustomers;
        this.customersGrid.DataBind();
    }
```

 <span data-ttu-id="eeb91-474">的代價就是產生的存放區命令永遠會篩選具有 null 檢查，但這些應該是相當簡單的資料庫伺服器，以最佳化：</span><span class="sxs-lookup"><span data-stu-id="eeb91-474">A tradeoff here is the generated store command will always have the filters with the null checks, but these should be fairly simple for the database server to optimize:</span></span>

``` SQL
...
WHERE ((0 = (CASE WHEN (@p__linq__1 IS NOT NULL) THEN cast(1 as bit) WHEN (@p__linq__1 IS NULL) THEN cast(0 as bit) END)) OR ([Project3].[C2] > @p__linq__2)) AND (@p__linq__3 IS NULL OR [Project3].[Country] = @p__linq__4)
```

### <a name="34-metadata-caching"></a><span data-ttu-id="eeb91-475">3.4 中繼資料快取</span><span class="sxs-lookup"><span data-stu-id="eeb91-475">3.4 Metadata caching</span></span>

<span data-ttu-id="eeb91-476">Entity Framework 也支援中繼資料快取。</span><span class="sxs-lookup"><span data-stu-id="eeb91-476">The Entity Framework also supports Metadata caching.</span></span> <span data-ttu-id="eeb91-477">這基本上跨不同的連線以相同的模型快取的類型資訊和資料庫型別對應資訊。</span><span class="sxs-lookup"><span data-stu-id="eeb91-477">This is essentially caching of type information and type-to-database mapping information across different connections to the same model.</span></span> <span data-ttu-id="eeb91-478">中繼資料快取是每個 AppDomain 唯一的。</span><span class="sxs-lookup"><span data-stu-id="eeb91-478">The Metadata cache is unique per AppDomain.</span></span>

#### <a name="341-metadata-caching-algorithm"></a><span data-ttu-id="eeb91-479">3.4.1 中繼資料快取演算法</span><span class="sxs-lookup"><span data-stu-id="eeb91-479">3.4.1 Metadata Caching algorithm</span></span>

1.  <span data-ttu-id="eeb91-480">模型的中繼資料資訊會儲存每個 EntityConnection ItemCollection 中。</span><span class="sxs-lookup"><span data-stu-id="eeb91-480">Metadata information for a model is stored in an ItemCollection for each EntityConnection.</span></span>
    -   <span data-ttu-id="eeb91-481">附帶一提，有不同的 ItemCollection 物件模型的不同部分。</span><span class="sxs-lookup"><span data-stu-id="eeb91-481">As a side note, there are different ItemCollection objects for different parts of the model.</span></span> <span data-ttu-id="eeb91-482">例如，StoreItemCollections 包含資料庫模型的相關資訊ObjectItemCollection 包含資料模型的相關資訊EdmItemCollection 包含概念模型的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="eeb91-482">For example, StoreItemCollections contains the information about the database model; ObjectItemCollection contains information about the data model; EdmItemCollection contains information about the conceptual model.</span></span>

2.  <span data-ttu-id="eeb91-483">如果兩個連線使用相同的連接字串，它們會共用相同的 ItemCollection 執行個體。</span><span class="sxs-lookup"><span data-stu-id="eeb91-483">If two connections use the same connection string, they will share the same ItemCollection instance.</span></span>
3.  <span data-ttu-id="eeb91-484">功能相同，但賦予不同的連接字串可能會導致不同的中繼資料快取。</span><span class="sxs-lookup"><span data-stu-id="eeb91-484">Functionally equivalent but textually different connection strings may result in different metadata caches.</span></span> <span data-ttu-id="eeb91-485">我們語彙基元化的連接字串，因此只要變更一個語彙基元順序應該會導致共用的中繼資料。</span><span class="sxs-lookup"><span data-stu-id="eeb91-485">We do tokenize connection strings, so simply changing the order of the tokens should result in shared metadata.</span></span> <span data-ttu-id="eeb91-486">但是，兩個功能似乎相同的連接字串可能不會評估為相同 token 化之後。</span><span class="sxs-lookup"><span data-stu-id="eeb91-486">But two connection strings that seem functionally the same may not be evaluated as identical after tokenization.</span></span>
4.  <span data-ttu-id="eeb91-487">ItemCollection 會定期檢查使用中。</span><span class="sxs-lookup"><span data-stu-id="eeb91-487">The ItemCollection is periodically checked for use.</span></span> <span data-ttu-id="eeb91-488">如果判斷該工作區未存取最近，它會標示為清理在下一步 的快取掃掠。</span><span class="sxs-lookup"><span data-stu-id="eeb91-488">If it is determined that a workspace has not been accessed recently, it will be marked for cleanup on the next cache sweep.</span></span>
5.  <span data-ttu-id="eeb91-489">只建立 EntityConnection 會 （但不會初始化中的項目集合，直到開啟連接） 建立的中繼資料快取。</span><span class="sxs-lookup"><span data-stu-id="eeb91-489">Merely creating an EntityConnection will cause a metadata cache to be created (though the item collections in it will not be initialized until the connection is opened).</span></span> <span data-ttu-id="eeb91-490">此工作區會保留於記憶體中，直到快取演算法可讓您決定不是 「 使用中 」。</span><span class="sxs-lookup"><span data-stu-id="eeb91-490">This workspace will remain in-memory until the caching algorithm determines it is not “in use”.</span></span>

<span data-ttu-id="eeb91-491">客戶諮詢小組已撰寫描述保存 ItemCollection 的參考，以使用大型模型時，避免 「 取代 」 的部落格文章： \< http://blogs.msdn.com/b/appfabriccat/archive/2010/10/22/metadataworkspace-reference-in-wcf-services.aspx>。</span><span class="sxs-lookup"><span data-stu-id="eeb91-491">The Customer Advisory Team has written a blog post that describes holding a reference to an ItemCollection in order to avoid "deprecation" when using large models: \<http://blogs.msdn.com/b/appfabriccat/archive/2010/10/22/metadataworkspace-reference-in-wcf-services.aspx>.</span></span>

#### <a name="342-the-relationship-between-metadata-caching-and-query-plan-caching"></a><span data-ttu-id="eeb91-492">3.4.2 中繼資料快取和查詢計畫快取之間的關聯性</span><span class="sxs-lookup"><span data-stu-id="eeb91-492">3.4.2 The relationship between Metadata Caching and Query Plan Caching</span></span>

<span data-ttu-id="eeb91-493">查詢計劃快取執行個體都存在於存放區類型的 MetadataWorkspace 的 ItemCollection。</span><span class="sxs-lookup"><span data-stu-id="eeb91-493">The query plan cache instance lives in the MetadataWorkspace's ItemCollection of store types.</span></span> <span data-ttu-id="eeb91-494">這表示快取存放區命令，將會用於針對任何的內容中使用給定的 MetadataWorkspace 具現化的查詢。</span><span class="sxs-lookup"><span data-stu-id="eeb91-494">This means that cached store commands will be used for queries against any context instantiated using a given MetadataWorkspace.</span></span> <span data-ttu-id="eeb91-495">這也表示，如果您有兩個連接字串會稍有不同，不符合 token 化之後，您將擁有不同的查詢計畫快取執行個體。</span><span class="sxs-lookup"><span data-stu-id="eeb91-495">It also means that if you have two connections strings that are slightly different and don't match after tokenizing, you will have different query plan cache instances.</span></span>

### <a name="35-results-caching"></a><span data-ttu-id="eeb91-496">3.5 快取的結果</span><span class="sxs-lookup"><span data-stu-id="eeb91-496">3.5 Results caching</span></span>

<span data-ttu-id="eeb91-497">結果快取 （也稱為 「 第二層快取），您可以保留查詢的結果在本機快取。</span><span class="sxs-lookup"><span data-stu-id="eeb91-497">With results caching (also known as "second-level caching"), you keep the results of queries in a local cache.</span></span> <span data-ttu-id="eeb91-498">發出查詢時，您先查看是否結果可在本機，再查詢對存放區。</span><span class="sxs-lookup"><span data-stu-id="eeb91-498">When issuing a query, you first see if the results are available locally before you query against the store.</span></span> <span data-ttu-id="eeb91-499">雖然快取的結果不直接支援 Entity framework，就可以使用包裝提供者中新增第二個層級快取。</span><span class="sxs-lookup"><span data-stu-id="eeb91-499">While results caching isn't directly supported by Entity Framework, it's possible to add a second level cache by using a wrapping provider.</span></span> <span data-ttu-id="eeb91-500">第二層快取的範例包裝提供者是 Alachisoft 的[Entity Framework 第二個層級快取基礎 NCache](http://www.alachisoft.com/ncache/entity-framework.html)。</span><span class="sxs-lookup"><span data-stu-id="eeb91-500">An example wrapping provider with a second-level cache is Alachisoft's [Entity Framework Second Level Cache based on NCache](http://www.alachisoft.com/ncache/entity-framework.html).</span></span>

<span data-ttu-id="eeb91-501">第二層快取這個實作會在評估 LINQ 運算式之後的位置 （和 funcletized） 插入功能，而且查詢執行計畫是計算，或從第一層快取擷取。</span><span class="sxs-lookup"><span data-stu-id="eeb91-501">This implementation of second-level caching is an injected functionality that takes place after the LINQ expression has been evaluated (and funcletized) and the query execution plan is computed or retrieved from the first-level cache.</span></span> <span data-ttu-id="eeb91-502">第二層快取會儲存只有未經處理的資料庫結果，因此具體化管線仍會執行之後。</span><span class="sxs-lookup"><span data-stu-id="eeb91-502">The second-level cache will then store only the raw database results, so the materialization pipeline still executes afterwards.</span></span>

#### <a name="351-additional-references-for-results-caching-with-the-wrapping-provider"></a><span data-ttu-id="eeb91-503">3.5.1 其他參考資料與換行的提供者快取的結果</span><span class="sxs-lookup"><span data-stu-id="eeb91-503">3.5.1 Additional references for results caching with the wrapping provider</span></span>

-   <span data-ttu-id="eeb91-504">Julie Lerman 寫過 < 第二層快取 Entity Framework 和 Windows Azure 中 「 MSDN 文件，包括如何更新範例包裝提供者，以使用 Windows Server AppFabric 快取： [https://msdn.microsoft.com/magazine/hh394143.aspx](https://msdn.microsoft.com/magazine/hh394143.aspx)</span><span class="sxs-lookup"><span data-stu-id="eeb91-504">Julie Lerman has written a "Second-Level Caching in Entity Framework and Windows Azure" MSDN article that includes how to update the sample wrapping provider to use Windows Server AppFabric caching: [https://msdn.microsoft.com/magazine/hh394143.aspx](https://msdn.microsoft.com/magazine/hh394143.aspx)</span></span>
-   <span data-ttu-id="eeb91-505">如果您正在使用 Entity Framework 5，在小組部落格有某篇文章說明如何使用 Entity Framework 5 的快取提供者執行的工作： \< http://blogs.msdn.com/b/adonet/archive/2010/09/13/ef-caching-with-jarek-kowalski-s-provider.aspx>。</span><span class="sxs-lookup"><span data-stu-id="eeb91-505">If you are working with Entity Framework 5, the team blog has a post that describes how to get things running with the caching provider for Entity Framework 5: \<http://blogs.msdn.com/b/adonet/archive/2010/09/13/ef-caching-with-jarek-kowalski-s-provider.aspx>.</span></span> <span data-ttu-id="eeb91-506">它也包含可協助執行自動化新增至您的專案第 2 個層級快取的 T4 範本。</span><span class="sxs-lookup"><span data-stu-id="eeb91-506">It also includes a T4 template to help automate adding the 2nd-level caching to your project.</span></span>

## <a name="4-autocompiled-queries"></a><span data-ttu-id="eeb91-507">4 個 Autocompiled 查詢</span><span class="sxs-lookup"><span data-stu-id="eeb91-507">4 Autocompiled Queries</span></span>

<span data-ttu-id="eeb91-508">當使用 Entity Framework 對資料庫發出查詢時，必須先經歷一系列的步驟之前實際具體化結果。這類的一個步驟就是查詢編譯。</span><span class="sxs-lookup"><span data-stu-id="eeb91-508">When a query is issued against a database using Entity Framework, it must go through a series of steps before actually materializing the results; one such step is Query Compilation.</span></span> <span data-ttu-id="eeb91-509">Entity SQL 查詢已知道有良好的效能，因為它們會自動快取，因此第二個或第三個階段執行相同的查詢就可以略過方案編譯器，並改為使用快取的計畫。</span><span class="sxs-lookup"><span data-stu-id="eeb91-509">Entity SQL queries were known to have good performance as they are automatically cached, so the second or third time you execute the same query it can skip the plan compiler and use the cached plan instead.</span></span>

<span data-ttu-id="eeb91-510">Entity Framework 5 引進了自動快取的 LINQ to Entities 查詢以及。</span><span class="sxs-lookup"><span data-stu-id="eeb91-510">Entity Framework 5 introduced automatic caching for LINQ to Entities queries as well.</span></span> <span data-ttu-id="eeb91-511">在過去版本的 Entity Framework 建立加快速度 CompiledQuery 您執行速度很常見的做法，因為這會讓您的 LINQ to Entities 查詢快取。</span><span class="sxs-lookup"><span data-stu-id="eeb91-511">In past editions of Entity Framework creating a CompiledQuery to speed your performance was a common practice, as this would make your LINQ to Entities query cacheable.</span></span> <span data-ttu-id="eeb91-512">因為快取會立即自動完成而不使用 CompiledQuery，我們將這項功能 「 autocompiled 查詢 」。</span><span class="sxs-lookup"><span data-stu-id="eeb91-512">Since caching is now done automatically without the use of a CompiledQuery, we call this feature “autocompiled queries”.</span></span> <span data-ttu-id="eeb91-513">如需有關查詢計畫快取和其機制的詳細資訊，請參閱查詢計畫快取。</span><span class="sxs-lookup"><span data-stu-id="eeb91-513">For more information about the query plan cache and its mechanics, see Query Plan Caching.</span></span>

<span data-ttu-id="eeb91-514">Entity Framework 會偵測，當查詢需要進行重新編譯，而且也這麼做，即使它已編譯之前，叫用的查詢時。</span><span class="sxs-lookup"><span data-stu-id="eeb91-514">Entity Framework detects when a query requires to be recompiled, and does so when the query is invoked even if it had been compiled before.</span></span> <span data-ttu-id="eeb91-515">會導致查詢重新編譯的常見狀況是：</span><span class="sxs-lookup"><span data-stu-id="eeb91-515">Common conditions that cause the query to be recompiled are:</span></span>

-   <span data-ttu-id="eeb91-516">變更與查詢相關聯 MergeOption。</span><span class="sxs-lookup"><span data-stu-id="eeb91-516">Changing the MergeOption associated to your query.</span></span> <span data-ttu-id="eeb91-517">將不會使用快取的查詢、 計劃編譯器將會再次執行而新建立的計畫快取。</span><span class="sxs-lookup"><span data-stu-id="eeb91-517">The cached query will not be used, instead the plan compiler will run again and the newly created plan gets cached.</span></span>
-   <span data-ttu-id="eeb91-518">變更 ContextOptions.UseCSharpNullComparisonBehavior 的值。</span><span class="sxs-lookup"><span data-stu-id="eeb91-518">Changing the value of ContextOptions.UseCSharpNullComparisonBehavior.</span></span> <span data-ttu-id="eeb91-519">您會取得變更 MergeOption 相同的效果。</span><span class="sxs-lookup"><span data-stu-id="eeb91-519">You get the same effect as changing the MergeOption.</span></span>

<span data-ttu-id="eeb91-520">其他條件可防止查詢使用快取。</span><span class="sxs-lookup"><span data-stu-id="eeb91-520">Other conditions can prevent your query from using the cache.</span></span> <span data-ttu-id="eeb91-521">常見範例包括：</span><span class="sxs-lookup"><span data-stu-id="eeb91-521">Common examples are:</span></span>

-   <span data-ttu-id="eeb91-522">使用 IEnumerable&lt;T&gt;。包含&lt;&gt;（T 值）。</span><span class="sxs-lookup"><span data-stu-id="eeb91-522">Using IEnumerable&lt;T&gt;.Contains&lt;&gt;(T value).</span></span>
-   <span data-ttu-id="eeb91-523">使用函式會產生常數的查詢。</span><span class="sxs-lookup"><span data-stu-id="eeb91-523">Using functions that produce queries with constants.</span></span>
-   <span data-ttu-id="eeb91-524">使用非對應的物件的屬性。</span><span class="sxs-lookup"><span data-stu-id="eeb91-524">Using the properties of a non-mapped object.</span></span>
-   <span data-ttu-id="eeb91-525">正在查詢連結到另一個需要重新編譯的查詢。</span><span class="sxs-lookup"><span data-stu-id="eeb91-525">Linking your query to another query that requires to be recompiled.</span></span>

### <a name="41-using-ienumerablelttgtcontainslttgtt-value"></a><span data-ttu-id="eeb91-526">4.1 使用 IEnumerable&lt;T&gt;。包含&lt;T&gt;（T 值）</span><span class="sxs-lookup"><span data-stu-id="eeb91-526">4.1 Using IEnumerable&lt;T&gt;.Contains&lt;T&gt;(T value)</span></span>

<span data-ttu-id="eeb91-527">Entity Framework 不會快取查詢，以叫用 IEnumerable&lt;T&gt;。包含&lt;T&gt;（T 值） 針對記憶體中集合，因為集合的值會視為 volatile。</span><span class="sxs-lookup"><span data-stu-id="eeb91-527">Entity Framework does not cache queries that invoke IEnumerable&lt;T&gt;.Contains&lt;T&gt;(T value) against an in-memory collection, since the values of the collection are considered volatile.</span></span> <span data-ttu-id="eeb91-528">下列範例查詢將不會快取，因此一律先處理計劃編譯器：</span><span class="sxs-lookup"><span data-stu-id="eeb91-528">The following example query will not be cached, so it will always be processed by the plan compiler:</span></span>

``` csharp
int[] ids = new int[10000];
...
using (var context = new MyContext())
{
    var query = context.MyEntities
                    .Where(entity => ids.Contains(entity.Id));

    var results = query.ToList();
    ...
}
```

<span data-ttu-id="eeb91-529">請注意，在執行其包含針對 IEnumerable 的大小決定如何快慢如何編譯您的查詢。</span><span class="sxs-lookup"><span data-stu-id="eeb91-529">Note that the size of the IEnumerable against which Contains is executed determines how fast or how slow your query is compiled.</span></span> <span data-ttu-id="eeb91-530">使用在上述範例所示的大型集合時，可以大幅降低效能。</span><span class="sxs-lookup"><span data-stu-id="eeb91-530">Performance can suffer significantly when using large collections such as the one shown in the example above.</span></span>

<span data-ttu-id="eeb91-531">Entity Framework 6 包含最佳化方式 IEnumerable&lt;T&gt;。包含&lt;T&gt;執行查詢時的運作方式 （T 值）。</span><span class="sxs-lookup"><span data-stu-id="eeb91-531">Entity Framework 6 contains optimizations to the way IEnumerable&lt;T&gt;.Contains&lt;T&gt;(T value) works when queries are executed.</span></span> <span data-ttu-id="eeb91-532">產生的 SQL 程式碼會產生更快和更容易閱讀，並在大部分情況下它也會執行更快在伺服器中。</span><span class="sxs-lookup"><span data-stu-id="eeb91-532">The SQL code that is generated is much faster to produce and more readable, and in most cases it also executes faster in the server.</span></span>

### <a name="42-using-functions-that-produce-queries-with-constants"></a><span data-ttu-id="eeb91-533">4.2 使用產生查詢與常數的函式</span><span class="sxs-lookup"><span data-stu-id="eeb91-533">4.2 Using functions that produce queries with constants</span></span>

<span data-ttu-id="eeb91-534">Skip （）、 take （）、 contains （） 和 DefautIfEmpty() LINQ 運算子不會產生含有參數的 SQL 查詢，但改為放以常數的形式傳遞給它們的值。</span><span class="sxs-lookup"><span data-stu-id="eeb91-534">The Skip(), Take(), Contains() and DefautIfEmpty() LINQ operators do not produce SQL queries with parameters but instead put the values passed to them as constants.</span></span> <span data-ttu-id="eeb91-535">基於這個原因，可能會完全相同的最後可能會到處查詢的查詢 EF 堆疊上和資料庫伺服器上 計畫快取，並執行未取得 reutilized 除非相同的常數用在後續的查詢執行中。</span><span class="sxs-lookup"><span data-stu-id="eeb91-535">Because of this, queries that might otherwise be identical end up polluting the query plan cache, both on the EF stack and on the database server, and do not get reutilized unless the same constants are used in a subsequent query execution.</span></span> <span data-ttu-id="eeb91-536">例如: </span><span class="sxs-lookup"><span data-stu-id="eeb91-536">For example:</span></span>

``` csharp
var id = 10;
...
using (var context = new MyContext())
{
    var query = context.MyEntities.Select(entity => entity.Id).Contains(id);

    var results = query.ToList();
    ...
}
```

<span data-ttu-id="eeb91-537">在此範例中，每次執行此查詢具有不同值的識別碼查詢將會編譯成新的計劃。</span><span class="sxs-lookup"><span data-stu-id="eeb91-537">In this example, each time this query is executed with a different value for id the query will be compiled into a new plan.</span></span>

<span data-ttu-id="eeb91-538">在特定注意的 Skip 和 Take 進行分頁時使用。</span><span class="sxs-lookup"><span data-stu-id="eeb91-538">In particular pay attention to the use of Skip and Take when doing paging.</span></span> <span data-ttu-id="eeb91-539">在 EF6 中這些方法會具有有效地讓快取的查詢計畫可重複使用因為 EF 可以擷取傳遞給這些方法的變數，並將它們轉譯成 SQLparameters lambda 多載。</span><span class="sxs-lookup"><span data-stu-id="eeb91-539">In EF6 these methods have a lambda overload that effectively makes the cached query plan reusable because EF can capture variables passed to these methods and translate them to SQLparameters.</span></span> <span data-ttu-id="eeb91-540">這也有助於保留快取更簡潔，因為否則每個查詢使用的不同的常數，Skip 和 Take 會取得自己的查詢計劃快取項目。</span><span class="sxs-lookup"><span data-stu-id="eeb91-540">This also helps keep the cache cleaner since otherwise each query with a different constant for Skip and Take would get its own query plan cache entry.</span></span>

<span data-ttu-id="eeb91-541">請考慮下列的程式碼，它是次佳，但只為了提供這個類別的查詢：</span><span class="sxs-lookup"><span data-stu-id="eeb91-541">Consider the following code, which is suboptimal but is only meant to exemplify this class of queries:</span></span>

``` csharp
var customers = context.Customers.OrderBy(c => c.LastName);
for (var i = 0; i < count; ++i)
{
    var currentCustomer = customers.Skip(i).FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

<span data-ttu-id="eeb91-542">這個相同的程式碼的更快速版本會牽涉到使用 lambda 呼叫略過：</span><span class="sxs-lookup"><span data-stu-id="eeb91-542">A faster version of this same code would involve calling Skip with a lambda:</span></span>

``` csharp
var customers = context.Customers.OrderBy(c => c.LastName);
for (var i = 0; i \< count; ++i)
{
    var currentCustomer = customers.Skip(() => i).FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

<span data-ttu-id="eeb91-543">第二個程式碼片段可能會因為每次執行查詢時，這可節省 CPU 時間，並避免查詢快取，會使用相同的查詢計畫執行速度快達 11%。</span><span class="sxs-lookup"><span data-stu-id="eeb91-543">The second snippet may run up to 11% faster because the same query plan is used every time the query is run, which saves CPU time and avoids polluting the query cache.</span></span> <span data-ttu-id="eeb91-544">此外，因為略過的參數是在 closure 中的程式碼可能也看起來像這樣現在：</span><span class="sxs-lookup"><span data-stu-id="eeb91-544">Furthermore, because the parameter to Skip is in a closure the code might as well look like this now:</span></span>

``` csharp
var i = 0;
var skippyCustomers = context.Customers.OrderBy(c => c.LastName).Skip(() => i);
for (; i < count; ++i)
{
    var currentCustomer = skippyCustomers.FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

### <a name="43-using-the-properties-of-a-non-mapped-object"></a><span data-ttu-id="eeb91-545">4.3 使用非對應的物件的屬性</span><span class="sxs-lookup"><span data-stu-id="eeb91-545">4.3 Using the properties of a non-mapped object</span></span>

<span data-ttu-id="eeb91-546">當查詢使用非對應的物件類型的屬性，因為參數，則將不會被快取。</span><span class="sxs-lookup"><span data-stu-id="eeb91-546">When a query uses the properties of a non-mapped object type as a parameter then the query will not get cached.</span></span> <span data-ttu-id="eeb91-547">例如: </span><span class="sxs-lookup"><span data-stu-id="eeb91-547">For example:</span></span>

``` csharp
using (var context = new MyContext())
{
    var myObject = new NonMappedType();

    var query = from entity in context.MyEntities
                where entity.Name.StartsWith(myObject.MyProperty)
                select entity;

   var results = query.ToList();
    ...
}
```

<span data-ttu-id="eeb91-548">在此範例中，假設類別 NonMappedType 不是實體模型的一部分。</span><span class="sxs-lookup"><span data-stu-id="eeb91-548">In this example, assume that class NonMappedType is not part of the Entity model.</span></span> <span data-ttu-id="eeb91-549">此查詢可以輕鬆地變更不使用非對應的型別，並改為使用本機變數，做為查詢的參數：</span><span class="sxs-lookup"><span data-stu-id="eeb91-549">This query can easily be changed to not use a non-mapped type and instead use a local variable as the parameter to the query:</span></span>

``` csharp
using (var context = new MyContext())
{
    var myObject = new NonMappedType();
    var myValue = myObject.MyProperty;
    var query = from entity in context.MyEntities
                where entity.Name.StartsWith(myValue)
                select entity;

    var results = query.ToList();
    ...
}
```

<span data-ttu-id="eeb91-550">在此情況下，查詢將能夠取得快取，並將受益於查詢計畫快取。</span><span class="sxs-lookup"><span data-stu-id="eeb91-550">In this case, the query will be able to get cached and will benefit from the query plan cache.</span></span>

### <a name="44-linking-to-queries-that-require-recompiling"></a><span data-ttu-id="eeb91-551">4.4 連結需要重新編譯的查詢</span><span class="sxs-lookup"><span data-stu-id="eeb91-551">4.4 Linking to queries that require recompiling</span></span>

<span data-ttu-id="eeb91-552">下列將上述相同的範例，如果您依賴需要重新編譯查詢的第二個查詢整個第二個查詢會也重新編譯。</span><span class="sxs-lookup"><span data-stu-id="eeb91-552">Following the same example as above, if you have a second query that relies on a query that needs to be recompiled, your entire second query will also be recompiled.</span></span> <span data-ttu-id="eeb91-553">以下是範例來說明此案例中：</span><span class="sxs-lookup"><span data-stu-id="eeb91-553">Here’s an example to illustrate this scenario:</span></span>

``` csharp
int[] ids = new int[10000];
...
using (var context = new MyContext())
{
    var firstQuery = from entity in context.MyEntities
                        where ids.Contains(entity.Id)
                        select entity;

    var secondQuery = from entity in context.MyEntities
                        where firstQuery.Any(otherEntity => otherEntity.Id == entity.Id)
                        select entity;

    var results = secondQuery.ToList();
    ...
}
```

<span data-ttu-id="eeb91-554">範例為泛型，但它也說明了如何連結至 firstQuery 造成 secondQuery 無法取得快取。</span><span class="sxs-lookup"><span data-stu-id="eeb91-554">The example is generic, but it illustrates how linking to firstQuery is causing secondQuery to be unable to get cached.</span></span> <span data-ttu-id="eeb91-555">如果 firstQuery 已經不需要重新編譯的查詢，然後 secondQuery 就已快取。</span><span class="sxs-lookup"><span data-stu-id="eeb91-555">If firstQuery had not been a query that requires recompiling, then secondQuery would have been cached.</span></span>

## <a name="5-notracking-queries"></a><span data-ttu-id="eeb91-556">5 NoTracking 查詢</span><span class="sxs-lookup"><span data-stu-id="eeb91-556">5 NoTracking Queries</span></span>

### <a name="51-disabling-change-tracking-to-reduce-state-management-overhead"></a><span data-ttu-id="eeb91-557">5.1 停用變更追蹤來減少狀態管理負荷</span><span class="sxs-lookup"><span data-stu-id="eeb91-557">5.1 Disabling change tracking to reduce state management overhead</span></span>

<span data-ttu-id="eeb91-558">如果您是在唯讀案例中，而且想要避免 ObjectStateManager 中載入之物件的額外負荷，您可以發出 「 追蹤 」 的查詢。</span><span class="sxs-lookup"><span data-stu-id="eeb91-558">If you are in a read-only scenario and want to avoid the overhead of loading the objects into the ObjectStateManager, you can issue "No Tracking" queries.</span></span>  <span data-ttu-id="eeb91-559">在查詢層級，可以停用變更追蹤。</span><span class="sxs-lookup"><span data-stu-id="eeb91-559">Change tracking can be disabled at the query level.</span></span>

<span data-ttu-id="eeb91-560">請注意，停用變更追蹤，您會有效地關閉物件快取。</span><span class="sxs-lookup"><span data-stu-id="eeb91-560">Note though that by disabling change tracking you are effectively turning off the object cache.</span></span> <span data-ttu-id="eeb91-561">當您查詢實體時，我們無法從 ObjectStateManager 提取先前具體化查詢結果來略過具體化。</span><span class="sxs-lookup"><span data-stu-id="eeb91-561">When you query for an entity, we can't skip materialization by pulling the previously-materialized query results from the ObjectStateManager.</span></span> <span data-ttu-id="eeb91-562">如果您要重複查詢相同的實體，在相同的內容，您實際上可能會看到受益於啟用變更追蹤效能。</span><span class="sxs-lookup"><span data-stu-id="eeb91-562">If you are repeatedly querying for the same entities on the same context, you might actually see a performance benefit from enabling change tracking.</span></span>

<span data-ttu-id="eeb91-563">當查詢使用 ObjectContext，ObjectQuery 和 ObjectSet 執行個體將會記住 MergeOption，一旦設定，並在其上所組成的查詢將會繼承父系查詢有效 MergeOption。</span><span class="sxs-lookup"><span data-stu-id="eeb91-563">When querying using ObjectContext, ObjectQuery and ObjectSet instances will remember a MergeOption once it is set, and queries that are composed on them will inherit the effective MergeOption of the parent query.</span></span> <span data-ttu-id="eeb91-564">當使用 DbContext，則可以停用追蹤 DbSet 上呼叫 AsNoTracking() 修飾詞。</span><span class="sxs-lookup"><span data-stu-id="eeb91-564">When using DbContext, tracking can be disabled by calling the AsNoTracking() modifier on the DbSet.</span></span>

#### <a name="511-disabling-change-tracking-for-a-query-when-using-dbcontext"></a><span data-ttu-id="eeb91-565">5.1.1 停用變更追蹤查詢，使用 DbContext 時</span><span class="sxs-lookup"><span data-stu-id="eeb91-565">5.1.1 Disabling change tracking for a query when using DbContext</span></span>

<span data-ttu-id="eeb91-566">您可以切換到 NoTracking 的查詢模式，由鏈結 AsNoTracking() 方法，在查詢中呼叫。</span><span class="sxs-lookup"><span data-stu-id="eeb91-566">You can switch the mode of a query to NoTracking by chaining a call to the AsNoTracking() method in the query.</span></span> <span data-ttu-id="eeb91-567">不同 ObjectQuery，DbContext API 的 DbSet 和 DbQuery 類別沒有可變動的屬性為 MergeOption。</span><span class="sxs-lookup"><span data-stu-id="eeb91-567">Unlike ObjectQuery, the DbSet and DbQuery classes in the DbContext API don’t have a mutable property for the MergeOption.</span></span>

``` csharp
    var productsForCategory = from p in context.Products.AsNoTracking()
                                where p.Category.CategoryName == selectedCategory
                                select p;


```

#### <a name="512-disabling-change-tracking-at-the-query-level-using-objectcontext"></a><span data-ttu-id="eeb91-568">5.1.2 停用變更追蹤使用 ObjectContext 的查詢層級</span><span class="sxs-lookup"><span data-stu-id="eeb91-568">5.1.2 Disabling change tracking at the query level using ObjectContext</span></span>

``` csharp
    var productsForCategory = from p in context.Products
                                where p.Category.CategoryName == selectedCategory
                                select p;

    ((ObjectQuery)productsForCategory).MergeOption = MergeOption.NoTracking;
```

#### <a name="513-disabling-change-tracking-for-an-entire-entity-set-using-objectcontext"></a><span data-ttu-id="eeb91-569">5.1.3 停用變更追蹤整個實體集使用 ObjectContext</span><span class="sxs-lookup"><span data-stu-id="eeb91-569">5.1.3 Disabling change tracking for an entire entity set using ObjectContext</span></span>

``` csharp
    context.Products.MergeOption = MergeOption.NoTracking;

    var productsForCategory = from p in context.Products
                                where p.Category.CategoryName == selectedCategory
                                select p;
```

### <a name="52-test-metrics-demonstrating-the-performance-benefit-of-notracking-queries"></a><span data-ttu-id="eeb91-570">5.2 測試示範 NoTracking 查詢的效能優勢的度量</span><span class="sxs-lookup"><span data-stu-id="eeb91-570">5.2 Test Metrics demonstrating the performance benefit of NoTracking queries</span></span>

<span data-ttu-id="eeb91-571">在這項測試，我們討論代價是藉由比較追蹤 NoTracking Navision 模型的查詢填入 ObjectStateManager。</span><span class="sxs-lookup"><span data-stu-id="eeb91-571">In this test we look at the cost of filling the ObjectStateManager by comparing Tracking to NoTracking queries for the Navision model.</span></span> <span data-ttu-id="eeb91-572">請參閱附錄 Navision 模型所執行的查詢類型的描述。</span><span class="sxs-lookup"><span data-stu-id="eeb91-572">See the appendix for a description of the Navision model and the types of queries which were executed.</span></span> <span data-ttu-id="eeb91-573">在此測試中，我們會逐一查看查詢的清單，並一次執行每一個。</span><span class="sxs-lookup"><span data-stu-id="eeb91-573">In this test, we iterate through the list of queries and execute each one once.</span></span> <span data-ttu-id="eeb91-574">我們使用"AppendOnly 」 的預設合併選項執行兩種變化的測試使用 NoTracking 查詢一次，一次。</span><span class="sxs-lookup"><span data-stu-id="eeb91-574">We ran two variations of the test, once with NoTracking queries and once with the default merge option of "AppendOnly".</span></span> <span data-ttu-id="eeb91-575">我們執行了每個變化 3 次，並且需要執行的平均值。</span><span class="sxs-lookup"><span data-stu-id="eeb91-575">We ran each variation 3 times and take the mean value of the runs.</span></span> <span data-ttu-id="eeb91-576">測試之間我們會清除 SQL Server 上的查詢快取，並壓縮 tempdb 執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="eeb91-576">Between the tests we clear the query cache on the SQL Server and shrink the tempdb by running the following commands:</span></span>

1.  <span data-ttu-id="eeb91-577">DBCC DROPCLEANBUFFERS</span><span class="sxs-lookup"><span data-stu-id="eeb91-577">DBCC DROPCLEANBUFFERS</span></span>
2.  <span data-ttu-id="eeb91-578">DBCC FREEPROCCACHE</span><span class="sxs-lookup"><span data-stu-id="eeb91-578">DBCC FREEPROCCACHE</span></span>
3.  <span data-ttu-id="eeb91-579">DBCC SHRINKDATABASE (tempdb，0)</span><span class="sxs-lookup"><span data-stu-id="eeb91-579">DBCC SHRINKDATABASE (tempdb, 0)</span></span>

<span data-ttu-id="eeb91-580">測試結果、 中位數超過 3 個執行：</span><span class="sxs-lookup"><span data-stu-id="eeb91-580">Test Results, median over 3 runs:</span></span>

|                        | <span data-ttu-id="eeb91-581">不追蹤-工作集</span><span class="sxs-lookup"><span data-stu-id="eeb91-581">NO TRACKING – WORKING SET</span></span> | <span data-ttu-id="eeb91-582">不是追蹤 – 時間</span><span class="sxs-lookup"><span data-stu-id="eeb91-582">NO TRACKING – TIME</span></span> | <span data-ttu-id="eeb91-583">附加只-工作集</span><span class="sxs-lookup"><span data-stu-id="eeb91-583">APPEND ONLY – WORKING SET</span></span> | <span data-ttu-id="eeb91-584">附加唯一 – 時間</span><span class="sxs-lookup"><span data-stu-id="eeb91-584">APPEND ONLY – TIME</span></span> |
|:-----------------------|:--------------------------|:-------------------|:--------------------------|:-------------------|
| <span data-ttu-id="eeb91-585">**Entity Framework 5**</span><span class="sxs-lookup"><span data-stu-id="eeb91-585">**Entity Framework 5**</span></span> | <span data-ttu-id="eeb91-586">460361728</span><span class="sxs-lookup"><span data-stu-id="eeb91-586">460361728</span></span>                 | <span data-ttu-id="eeb91-587">1163536 ms</span><span class="sxs-lookup"><span data-stu-id="eeb91-587">1163536 ms</span></span>         | <span data-ttu-id="eeb91-588">596545536</span><span class="sxs-lookup"><span data-stu-id="eeb91-588">596545536</span></span>                 | <span data-ttu-id="eeb91-589">1273042 ms</span><span class="sxs-lookup"><span data-stu-id="eeb91-589">1273042 ms</span></span>         |
| <span data-ttu-id="eeb91-590">**Entity Framework 6**</span><span class="sxs-lookup"><span data-stu-id="eeb91-590">**Entity Framework 6**</span></span> | <span data-ttu-id="eeb91-591">647127040</span><span class="sxs-lookup"><span data-stu-id="eeb91-591">647127040</span></span>                 | <span data-ttu-id="eeb91-592">190228 ms</span><span class="sxs-lookup"><span data-stu-id="eeb91-592">190228 ms</span></span>          | <span data-ttu-id="eeb91-593">832798720</span><span class="sxs-lookup"><span data-stu-id="eeb91-593">832798720</span></span>                 | <span data-ttu-id="eeb91-594">195521 ms</span><span class="sxs-lookup"><span data-stu-id="eeb91-594">195521 ms</span></span>          |

<span data-ttu-id="eeb91-595">Entity Framework 5 將會有較少的記憶體執行結尾比 Entity Framework 6。</span><span class="sxs-lookup"><span data-stu-id="eeb91-595">Entity Framework 5 will have a smaller memory footprint at the end of the run than Entity Framework 6 does.</span></span> <span data-ttu-id="eeb91-596">Entity Framework 6 所耗用的額外記憶體是額外的記憶體結構和啟用新功能和更佳的效能的程式碼的結果。</span><span class="sxs-lookup"><span data-stu-id="eeb91-596">The additional memory consumed by Entity Framework 6 is the result of additional memory structures and code that enable new features and better performance.</span></span>

<span data-ttu-id="eeb91-597">使用 ObjectStateManager 時，也是產生的記憶體耗用量的清除差異。</span><span class="sxs-lookup"><span data-stu-id="eeb91-597">There’s also a clear difference in memory footprint when using the ObjectStateManager.</span></span> <span data-ttu-id="eeb91-598">Entity Framework 5 30%的增加其使用量，當追蹤時我們從資料庫具體化的所有實體。</span><span class="sxs-lookup"><span data-stu-id="eeb91-598">Entity Framework 5 increased its footprint by 30% when keeping track of all the entities we materialized from the database.</span></span> <span data-ttu-id="eeb91-599">這麼做時，entity Framework 6 會增加其使用量的 28%。</span><span class="sxs-lookup"><span data-stu-id="eeb91-599">Entity Framework 6 increased its footprint by 28% when doing so.</span></span>

<span data-ttu-id="eeb91-600">大量時間，Entity Framework 6 會勝過 Entity Framework 5，在這項測試由大型的邊界。</span><span class="sxs-lookup"><span data-stu-id="eeb91-600">In terms of time, Entity Framework 6 outperforms Entity Framework 5 in this test by a large margin.</span></span> <span data-ttu-id="eeb91-601">Entity Framework 6 完成大約 16%的時間裡由 Entity Framework 5 中的測試。</span><span class="sxs-lookup"><span data-stu-id="eeb91-601">Entity Framework 6 completed the test in roughly 16% of the time consumed by Entity Framework 5.</span></span> <span data-ttu-id="eeb91-602">此外，Entity Framework 5 會需要更多的 9%的時間，才能完成 ObjectStateManager 為使用中時。</span><span class="sxs-lookup"><span data-stu-id="eeb91-602">Additionally, Entity Framework 5 takes 9% more time to complete when the ObjectStateManager is being used.</span></span> <span data-ttu-id="eeb91-603">相較之下，Entity Framework 6 使用更多時間使用 ObjectStateManager 的 3%。</span><span class="sxs-lookup"><span data-stu-id="eeb91-603">In comparison, Entity Framework 6 is using 3% more time when using the ObjectStateManager.</span></span>

## <a name="6-query-execution-options"></a><span data-ttu-id="eeb91-604">6 個的查詢執行選項</span><span class="sxs-lookup"><span data-stu-id="eeb91-604">6 Query Execution Options</span></span>

<span data-ttu-id="eeb91-605">Entity Framework 提供數個不同的方式，來查詢。</span><span class="sxs-lookup"><span data-stu-id="eeb91-605">Entity Framework offers several different ways to query.</span></span> <span data-ttu-id="eeb91-606">我們將看看下列的選項、 比較各自的優缺點，並檢查其效能特性：</span><span class="sxs-lookup"><span data-stu-id="eeb91-606">We'll take a look at the following options, compare the pros and cons of each, and examine their performance characteristics:</span></span>

-   <span data-ttu-id="eeb91-607">LINQ to Entities。</span><span class="sxs-lookup"><span data-stu-id="eeb91-607">LINQ to Entities.</span></span>
-   <span data-ttu-id="eeb91-608">不是追蹤 LINQ to Entities。</span><span class="sxs-lookup"><span data-stu-id="eeb91-608">No Tracking LINQ to Entities.</span></span>
-   <span data-ttu-id="eeb91-609">Entity SQL 的 ObjectQuery。</span><span class="sxs-lookup"><span data-stu-id="eeb91-609">Entity SQL over an ObjectQuery.</span></span>
-   <span data-ttu-id="eeb91-610">Entity SQL 的 EntityCommand。</span><span class="sxs-lookup"><span data-stu-id="eeb91-610">Entity SQL over an EntityCommand.</span></span>
-   <span data-ttu-id="eeb91-611">ExecuteStoreQuery。</span><span class="sxs-lookup"><span data-stu-id="eeb91-611">ExecuteStoreQuery.</span></span>
-   <span data-ttu-id="eeb91-612">SqlQuery。</span><span class="sxs-lookup"><span data-stu-id="eeb91-612">SqlQuery.</span></span>
-   <span data-ttu-id="eeb91-613">CompiledQuery。</span><span class="sxs-lookup"><span data-stu-id="eeb91-613">CompiledQuery.</span></span>

### <a name="61-------linq-to-entities-queries"></a><span data-ttu-id="eeb91-614">6.1 LINQ to Entities 查詢</span><span class="sxs-lookup"><span data-stu-id="eeb91-614">6.1       LINQ to Entities queries</span></span>

``` csharp
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
```

<span data-ttu-id="eeb91-615">**專業人員**</span><span class="sxs-lookup"><span data-stu-id="eeb91-615">**Pros**</span></span>

-   <span data-ttu-id="eeb91-616">適用於 CUD 作業。</span><span class="sxs-lookup"><span data-stu-id="eeb91-616">Suitable for CUD operations.</span></span>
-   <span data-ttu-id="eeb91-617">完全具體化的物件。</span><span class="sxs-lookup"><span data-stu-id="eeb91-617">Fully materialized objects.</span></span>
-   <span data-ttu-id="eeb91-618">簡單寫入與內建的程式設計語言的語法。</span><span class="sxs-lookup"><span data-stu-id="eeb91-618">Simplest to write with syntax built into the programming language.</span></span>
-   <span data-ttu-id="eeb91-619">良好的效能。</span><span class="sxs-lookup"><span data-stu-id="eeb91-619">Good performance.</span></span>

<span data-ttu-id="eeb91-620">**缺點**</span><span class="sxs-lookup"><span data-stu-id="eeb91-620">**Cons**</span></span>

-   <span data-ttu-id="eeb91-621">某些技術的限制，例如：</span><span class="sxs-lookup"><span data-stu-id="eeb91-621">Certain technical restrictions, such as:</span></span>
    -   <span data-ttu-id="eeb91-622">使用 DefaultIfEmpty OUTER JOIN 的查詢模式會導致更複雜的查詢比簡單的外部聯結陳述式，在 Entity SQL。</span><span class="sxs-lookup"><span data-stu-id="eeb91-622">Patterns using DefaultIfEmpty for OUTER JOIN queries result in more complex queries than simple OUTER JOIN statements in Entity SQL.</span></span>
    -   <span data-ttu-id="eeb91-623">您仍然無法使用 LIKE 與一般的模式比對。</span><span class="sxs-lookup"><span data-stu-id="eeb91-623">You still can’t use LIKE with general pattern matching.</span></span>

### <a name="62-------no-tracking-linq-to-entities-queries"></a><span data-ttu-id="eeb91-624">6.2 沒有追蹤 LINQ to Entities 查詢</span><span class="sxs-lookup"><span data-stu-id="eeb91-624">6.2       No Tracking LINQ to Entities queries</span></span>

<span data-ttu-id="eeb91-625">當內容衍生的 ObjectContext:</span><span class="sxs-lookup"><span data-stu-id="eeb91-625">When the context derives ObjectContext:</span></span>

``` csharp
context.Products.MergeOption = MergeOption.NoTracking;
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
```

<span data-ttu-id="eeb91-626">當內容衍生 DbContext:</span><span class="sxs-lookup"><span data-stu-id="eeb91-626">When the context derives DbContext:</span></span>

``` csharp
var q = context.Products.AsNoTracking()
                        .Where(p => p.Category.CategoryName == "Beverages");
```

<span data-ttu-id="eeb91-627">**專業人員**</span><span class="sxs-lookup"><span data-stu-id="eeb91-627">**Pros**</span></span>

-   <span data-ttu-id="eeb91-628">透過一般 LINQ 查詢改善的效能。</span><span class="sxs-lookup"><span data-stu-id="eeb91-628">Improved performance over regular LINQ queries.</span></span>
-   <span data-ttu-id="eeb91-629">完全具體化的物件。</span><span class="sxs-lookup"><span data-stu-id="eeb91-629">Fully materialized objects.</span></span>
-   <span data-ttu-id="eeb91-630">簡單寫入與內建的程式設計語言的語法。</span><span class="sxs-lookup"><span data-stu-id="eeb91-630">Simplest to write with syntax built into the programming language.</span></span>

<span data-ttu-id="eeb91-631">**缺點**</span><span class="sxs-lookup"><span data-stu-id="eeb91-631">**Cons**</span></span>

-   <span data-ttu-id="eeb91-632">不適合用於 CUD 作業。</span><span class="sxs-lookup"><span data-stu-id="eeb91-632">Not suitable for CUD operations.</span></span>
-   <span data-ttu-id="eeb91-633">某些技術的限制，例如：</span><span class="sxs-lookup"><span data-stu-id="eeb91-633">Certain technical restrictions, such as:</span></span>
    -   <span data-ttu-id="eeb91-634">使用 DefaultIfEmpty OUTER JOIN 的查詢模式會導致更複雜的查詢比簡單的外部聯結陳述式，在 Entity SQL。</span><span class="sxs-lookup"><span data-stu-id="eeb91-634">Patterns using DefaultIfEmpty for OUTER JOIN queries result in more complex queries than simple OUTER JOIN statements in Entity SQL.</span></span>
    -   <span data-ttu-id="eeb91-635">您仍然無法使用 LIKE 與一般的模式比對。</span><span class="sxs-lookup"><span data-stu-id="eeb91-635">You still can’t use LIKE with general pattern matching.</span></span>

<span data-ttu-id="eeb91-636">請注意即使 NoTracking 未指定，則不會追蹤專案純量屬性的查詢。</span><span class="sxs-lookup"><span data-stu-id="eeb91-636">Note that queries that project scalar properties are not tracked even if the NoTracking is not specified.</span></span> <span data-ttu-id="eeb91-637">例如: </span><span class="sxs-lookup"><span data-stu-id="eeb91-637">For example:</span></span>

``` csharp
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages").Select(p => new { p.ProductName });
```

<span data-ttu-id="eeb91-638">這個特定的查詢未明確指定所 NoTracking，但因為它不會具體化有已知的物件狀態管理員然後具體化的結果型別不會追蹤。</span><span class="sxs-lookup"><span data-stu-id="eeb91-638">This particular query doesn’t explicitly specify being NoTracking, but since it’s not materializing a type that’s known to the object state manager then the materialized result is not tracked.</span></span>

### <a name="63-------entity-sql-over-an-objectquery"></a><span data-ttu-id="eeb91-639">6.3 entity SQL 的 ObjectQuery</span><span class="sxs-lookup"><span data-stu-id="eeb91-639">6.3       Entity SQL over an ObjectQuery</span></span>

``` csharp
ObjectQuery<Product> products = context.Products.Where("it.Category.CategoryName = 'Beverages'");
```

<span data-ttu-id="eeb91-640">**專業人員**</span><span class="sxs-lookup"><span data-stu-id="eeb91-640">**Pros**</span></span>

-   <span data-ttu-id="eeb91-641">適用於 CUD 作業。</span><span class="sxs-lookup"><span data-stu-id="eeb91-641">Suitable for CUD operations.</span></span>
-   <span data-ttu-id="eeb91-642">完全具體化的物件。</span><span class="sxs-lookup"><span data-stu-id="eeb91-642">Fully materialized objects.</span></span>
-   <span data-ttu-id="eeb91-643">支援查詢計畫快取。</span><span class="sxs-lookup"><span data-stu-id="eeb91-643">Supports query plan caching.</span></span>

<span data-ttu-id="eeb91-644">**缺點**</span><span class="sxs-lookup"><span data-stu-id="eeb91-644">**Cons**</span></span>

-   <span data-ttu-id="eeb91-645">包含文字的查詢字串，其為更容易發生使用者錯誤比語言內建的查詢建構。</span><span class="sxs-lookup"><span data-stu-id="eeb91-645">Involves textual query strings which are more prone to user error than query constructs built into the language.</span></span>

### <a name="64-------entity-sql-over-an-entity-command"></a><span data-ttu-id="eeb91-646">6.4 entity SQL 的實體命令</span><span class="sxs-lookup"><span data-stu-id="eeb91-646">6.4       Entity SQL over an Entity Command</span></span>

``` csharp
EntityCommand cmd = eConn.CreateCommand();
cmd.CommandText = "Select p From NorthwindEntities.Products As p Where p.Category.CategoryName = 'Beverages'";

using (EntityDataReader reader = cmd.ExecuteReader(CommandBehavior.SequentialAccess))
{
    while (reader.Read())
    {
        // manually 'materialize' the product
    }
}
```

<span data-ttu-id="eeb91-647">**專業人員**</span><span class="sxs-lookup"><span data-stu-id="eeb91-647">**Pros**</span></span>

-   <span data-ttu-id="eeb91-648">支援查詢計畫快取中 （計畫快取會支援.NET 4.5 中的所有其他查詢類型） 的.NET 4.0。</span><span class="sxs-lookup"><span data-stu-id="eeb91-648">Supports query plan caching in .NET 4.0 (plan caching is supported by all other query types in .NET 4.5).</span></span>

<span data-ttu-id="eeb91-649">**缺點**</span><span class="sxs-lookup"><span data-stu-id="eeb91-649">**Cons**</span></span>

-   <span data-ttu-id="eeb91-650">包含文字的查詢字串，其為更容易發生使用者錯誤比語言內建的查詢建構。</span><span class="sxs-lookup"><span data-stu-id="eeb91-650">Involves textual query strings which are more prone to user error than query constructs built into the language.</span></span>
-   <span data-ttu-id="eeb91-651">不適合用於 CUD 作業。</span><span class="sxs-lookup"><span data-stu-id="eeb91-651">Not suitable for CUD operations.</span></span>
-   <span data-ttu-id="eeb91-652">結果會不會自動具體化，而且必須從 資料讀取器讀取。</span><span class="sxs-lookup"><span data-stu-id="eeb91-652">Results are not automatically materialized, and must be read from the data reader.</span></span>

### <a name="65-------sqlquery-and-executestorequery"></a><span data-ttu-id="eeb91-653">6.5 SqlQuery 和 ExecuteStoreQuery</span><span class="sxs-lookup"><span data-stu-id="eeb91-653">6.5       SqlQuery and ExecuteStoreQuery</span></span>

<span data-ttu-id="eeb91-654">在資料庫上 SqlQuery:</span><span class="sxs-lookup"><span data-stu-id="eeb91-654">SqlQuery on Database:</span></span>

``` csharp
// use this to obtain entities and not track them
var q1 = context.Database.SqlQuery<Product>("select * from products");
```

<span data-ttu-id="eeb91-655">SqlQuery DbSet 上：</span><span class="sxs-lookup"><span data-stu-id="eeb91-655">SqlQuery on DbSet:</span></span>

``` csharp
// use this to obtain entities and have them tracked
var q2 = context.Products.SqlQuery("select * from products");
```

<span data-ttu-id="eeb91-656">ExecyteStoreQuery:</span><span class="sxs-lookup"><span data-stu-id="eeb91-656">ExecyteStoreQuery:</span></span>

``` csharp
var beverages = context.ExecuteStoreQuery<Product>(
@"     SELECT        P.ProductID, P.ProductName, P.SupplierID, P.CategoryID, P.QuantityPerUnit, P.UnitPrice, P.UnitsInStock, P.UnitsOnOrder, P.ReorderLevel, P.Discontinued, P.DiscontinuedDate
       FROM            Products AS P INNER JOIN Categories AS C ON P.CategoryID = C.CategoryID
       WHERE        (C.CategoryName = 'Beverages')"
);
```

<span data-ttu-id="eeb91-657">**專業人員**</span><span class="sxs-lookup"><span data-stu-id="eeb91-657">**Pros**</span></span>

-   <span data-ttu-id="eeb91-658">通常最快的效能因為計劃編譯器會略過。</span><span class="sxs-lookup"><span data-stu-id="eeb91-658">Generally fastest performance since plan compiler is bypassed.</span></span>
-   <span data-ttu-id="eeb91-659">完全具體化的物件。</span><span class="sxs-lookup"><span data-stu-id="eeb91-659">Fully materialized objects.</span></span>
-   <span data-ttu-id="eeb91-660">適用於 CUD 作業使用從 DbSet 時。</span><span class="sxs-lookup"><span data-stu-id="eeb91-660">Suitable for CUD operations when used from the DbSet.</span></span>

<span data-ttu-id="eeb91-661">**缺點**</span><span class="sxs-lookup"><span data-stu-id="eeb91-661">**Cons**</span></span>

-   <span data-ttu-id="eeb91-662">查詢是文字又容易出錯。</span><span class="sxs-lookup"><span data-stu-id="eeb91-662">Query is textual and error prone.</span></span>
-   <span data-ttu-id="eeb91-663">查詢會繫結至特定的後端，使用存放區語意，而不是概念性的語意。</span><span class="sxs-lookup"><span data-stu-id="eeb91-663">Query is tied to a specific backend by using store semantics instead of conceptual semantics.</span></span>
-   <span data-ttu-id="eeb91-664">有繼承時，手動的查詢需要處理要求的型別對應條件。</span><span class="sxs-lookup"><span data-stu-id="eeb91-664">When inheritance is present, handcrafted query needs to account for mapping conditions for the type requested.</span></span>

### <a name="66-------compiledquery"></a><span data-ttu-id="eeb91-665">6.6 CompiledQuery</span><span class="sxs-lookup"><span data-stu-id="eeb91-665">6.6       CompiledQuery</span></span>

``` csharp
private static readonly Func<NorthwindEntities, string, IQueryable<Product>> productsForCategoryCQ = CompiledQuery.Compile(
    (NorthwindEntities context, string categoryName) =>
        context.Products.Where(p => p.Category.CategoryName == categoryName)
        );
…
var q = context.InvokeProductsForCategoryCQ("Beverages");
```

<span data-ttu-id="eeb91-666">**專業人員**</span><span class="sxs-lookup"><span data-stu-id="eeb91-666">**Pros**</span></span>

-   <span data-ttu-id="eeb91-667">透過一般 LINQ 查詢，以提供最多 7%的效能改進。</span><span class="sxs-lookup"><span data-stu-id="eeb91-667">Provides up to a 7% performance improvement over regular LINQ queries.</span></span>
-   <span data-ttu-id="eeb91-668">完全具體化的物件。</span><span class="sxs-lookup"><span data-stu-id="eeb91-668">Fully materialized objects.</span></span>
-   <span data-ttu-id="eeb91-669">適用於 CUD 作業。</span><span class="sxs-lookup"><span data-stu-id="eeb91-669">Suitable for CUD operations.</span></span>

<span data-ttu-id="eeb91-670">**缺點**</span><span class="sxs-lookup"><span data-stu-id="eeb91-670">**Cons**</span></span>

-   <span data-ttu-id="eeb91-671">增加複雜度和程式設計的額外負荷。</span><span class="sxs-lookup"><span data-stu-id="eeb91-671">Increased complexity and programming overhead.</span></span>
-   <span data-ttu-id="eeb91-672">在已編譯的查詢上撰寫時，遺失的效能改善。</span><span class="sxs-lookup"><span data-stu-id="eeb91-672">The performance improvement is lost when composing on top of a compiled query.</span></span>
-   <span data-ttu-id="eeb91-673">無法為 CompiledQuery-例如，匿名型別的投影寫入一些 LINQ 查詢。</span><span class="sxs-lookup"><span data-stu-id="eeb91-673">Some LINQ queries can't be written as a CompiledQuery - for example, projections of anonymous types.</span></span>

### <a name="67-------performance-comparison-of-different-query-options"></a><span data-ttu-id="eeb91-674">6.7 不同的查詢選項效能比較</span><span class="sxs-lookup"><span data-stu-id="eeb91-674">6.7       Performance Comparison of different query options</span></span>

<span data-ttu-id="eeb91-675">其中內容建立而不逾時的簡單 microbenchmarks 暫停測試。</span><span class="sxs-lookup"><span data-stu-id="eeb91-675">Simple microbenchmarks where the context creation was not timed were put to the test.</span></span> <span data-ttu-id="eeb91-676">我們測量 5000 次查詢的非快取在受控制的環境中的實體集。</span><span class="sxs-lookup"><span data-stu-id="eeb91-676">We measured querying 5000 times for a set of non-cached entities in a controlled environment.</span></span> <span data-ttu-id="eeb91-677">這些識別碼會採取與警告： 不會反映實際的應用程式中，所產生的數字，而它們是非常精確的測量的效能差異，當比較不同的查詢選項時，還有多少蘋果對蘋果，但不包括建立新的內容的成本。</span><span class="sxs-lookup"><span data-stu-id="eeb91-677">These numbers are to be taken with a warning: they do not reflect actual numbers produced by an application, but instead they are a very accurate measurement of how much of a performance difference there is when different querying options are compared apples-to-apples, excluding the cost of creating a new context.</span></span>

| <span data-ttu-id="eeb91-678">EF</span><span class="sxs-lookup"><span data-stu-id="eeb91-678">EF</span></span>  | <span data-ttu-id="eeb91-679">測試</span><span class="sxs-lookup"><span data-stu-id="eeb91-679">Test</span></span>                                 | <span data-ttu-id="eeb91-680">時間 （毫秒）</span><span class="sxs-lookup"><span data-stu-id="eeb91-680">Time (ms)</span></span> | <span data-ttu-id="eeb91-681">記憶體</span><span class="sxs-lookup"><span data-stu-id="eeb91-681">Memory</span></span>   |
|:----|:-------------------------------------|:----------|:---------|
| <span data-ttu-id="eeb91-682">EF5</span><span class="sxs-lookup"><span data-stu-id="eeb91-682">EF5</span></span> | <span data-ttu-id="eeb91-683">ObjectContext ESQL</span><span class="sxs-lookup"><span data-stu-id="eeb91-683">ObjectContext ESQL</span></span>                   | <span data-ttu-id="eeb91-684">2414</span><span class="sxs-lookup"><span data-stu-id="eeb91-684">2414</span></span>      | <span data-ttu-id="eeb91-685">38801408</span><span class="sxs-lookup"><span data-stu-id="eeb91-685">38801408</span></span> |
| <span data-ttu-id="eeb91-686">EF5</span><span class="sxs-lookup"><span data-stu-id="eeb91-686">EF5</span></span> | <span data-ttu-id="eeb91-687">ObjectContext Linq 查詢</span><span class="sxs-lookup"><span data-stu-id="eeb91-687">ObjectContext Linq Query</span></span>             | <span data-ttu-id="eeb91-688">2692</span><span class="sxs-lookup"><span data-stu-id="eeb91-688">2692</span></span>      | <span data-ttu-id="eeb91-689">38277120</span><span class="sxs-lookup"><span data-stu-id="eeb91-689">38277120</span></span> |
| <span data-ttu-id="eeb91-690">EF5</span><span class="sxs-lookup"><span data-stu-id="eeb91-690">EF5</span></span> | <span data-ttu-id="eeb91-691">DbContext Linq 查詢不追蹤</span><span class="sxs-lookup"><span data-stu-id="eeb91-691">DbContext Linq Query No Tracking</span></span>     | <span data-ttu-id="eeb91-692">2818</span><span class="sxs-lookup"><span data-stu-id="eeb91-692">2818</span></span>      | <span data-ttu-id="eeb91-693">41840640</span><span class="sxs-lookup"><span data-stu-id="eeb91-693">41840640</span></span> |
| <span data-ttu-id="eeb91-694">EF5</span><span class="sxs-lookup"><span data-stu-id="eeb91-694">EF5</span></span> | <span data-ttu-id="eeb91-695">DbContext Linq 查詢</span><span class="sxs-lookup"><span data-stu-id="eeb91-695">DbContext Linq Query</span></span>                 | <span data-ttu-id="eeb91-696">2930</span><span class="sxs-lookup"><span data-stu-id="eeb91-696">2930</span></span>      | <span data-ttu-id="eeb91-697">41771008</span><span class="sxs-lookup"><span data-stu-id="eeb91-697">41771008</span></span> |
| <span data-ttu-id="eeb91-698">EF5</span><span class="sxs-lookup"><span data-stu-id="eeb91-698">EF5</span></span> | <span data-ttu-id="eeb91-699">ObjectContext Linq 查詢不追蹤</span><span class="sxs-lookup"><span data-stu-id="eeb91-699">ObjectContext Linq Query No Tracking</span></span> | <span data-ttu-id="eeb91-700">3013</span><span class="sxs-lookup"><span data-stu-id="eeb91-700">3013</span></span>      | <span data-ttu-id="eeb91-701">38412288</span><span class="sxs-lookup"><span data-stu-id="eeb91-701">38412288</span></span> |
|     |                                      |           |          |
| <span data-ttu-id="eeb91-702">EF6</span><span class="sxs-lookup"><span data-stu-id="eeb91-702">EF6</span></span> | <span data-ttu-id="eeb91-703">ObjectContext ESQL</span><span class="sxs-lookup"><span data-stu-id="eeb91-703">ObjectContext ESQL</span></span>                   | <span data-ttu-id="eeb91-704">2059</span><span class="sxs-lookup"><span data-stu-id="eeb91-704">2059</span></span>      | <span data-ttu-id="eeb91-705">46039040</span><span class="sxs-lookup"><span data-stu-id="eeb91-705">46039040</span></span> |
| <span data-ttu-id="eeb91-706">EF6</span><span class="sxs-lookup"><span data-stu-id="eeb91-706">EF6</span></span> | <span data-ttu-id="eeb91-707">ObjectContext Linq 查詢</span><span class="sxs-lookup"><span data-stu-id="eeb91-707">ObjectContext Linq Query</span></span>             | <span data-ttu-id="eeb91-708">3074</span><span class="sxs-lookup"><span data-stu-id="eeb91-708">3074</span></span>      | <span data-ttu-id="eeb91-709">45248512</span><span class="sxs-lookup"><span data-stu-id="eeb91-709">45248512</span></span> |
| <span data-ttu-id="eeb91-710">EF6</span><span class="sxs-lookup"><span data-stu-id="eeb91-710">EF6</span></span> | <span data-ttu-id="eeb91-711">DbContext Linq 查詢不追蹤</span><span class="sxs-lookup"><span data-stu-id="eeb91-711">DbContext Linq Query No Tracking</span></span>     | <span data-ttu-id="eeb91-712">3125</span><span class="sxs-lookup"><span data-stu-id="eeb91-712">3125</span></span>      | <span data-ttu-id="eeb91-713">47575040</span><span class="sxs-lookup"><span data-stu-id="eeb91-713">47575040</span></span> |
| <span data-ttu-id="eeb91-714">EF6</span><span class="sxs-lookup"><span data-stu-id="eeb91-714">EF6</span></span> | <span data-ttu-id="eeb91-715">DbContext Linq 查詢</span><span class="sxs-lookup"><span data-stu-id="eeb91-715">DbContext Linq Query</span></span>                 | <span data-ttu-id="eeb91-716">3420</span><span class="sxs-lookup"><span data-stu-id="eeb91-716">3420</span></span>      | <span data-ttu-id="eeb91-717">47652864</span><span class="sxs-lookup"><span data-stu-id="eeb91-717">47652864</span></span> |
| <span data-ttu-id="eeb91-718">EF6</span><span class="sxs-lookup"><span data-stu-id="eeb91-718">EF6</span></span> | <span data-ttu-id="eeb91-719">ObjectContext Linq 查詢不追蹤</span><span class="sxs-lookup"><span data-stu-id="eeb91-719">ObjectContext Linq Query No Tracking</span></span> | <span data-ttu-id="eeb91-720">3593</span><span class="sxs-lookup"><span data-stu-id="eeb91-720">3593</span></span>      | <span data-ttu-id="eeb91-721">45260800</span><span class="sxs-lookup"><span data-stu-id="eeb91-721">45260800</span></span> |

![EF5 微基準測試，5000 暖的反覆項目](~/ef6/media/ef5micro5000warm.png)

![EF6 微基準測試，5000 暖的反覆項目](~/ef6/media/ef6micro5000warm.png)

<span data-ttu-id="eeb91-724">Microbenchmarks 非常非常敏感的程式碼的小型變更。</span><span class="sxs-lookup"><span data-stu-id="eeb91-724">Microbenchmarks are very sensitive to small changes in the code.</span></span> <span data-ttu-id="eeb91-725">在這個案例中，Entity Framework 5 的成本與 Entity Framework 6 之間的差異是由於新增[攔截](~/ef6/fundamentals/logging-and-interception.md)並[異動改進](~/ef6/saving/transactions.md)。</span><span class="sxs-lookup"><span data-stu-id="eeb91-725">In this case, the difference between the costs of Entity Framework 5 and Entity Framework 6 are due to the addition of [interception](~/ef6/fundamentals/logging-and-interception.md) and [transactional improvements](~/ef6/saving/transactions.md).</span></span> <span data-ttu-id="eeb91-726">這些 microbenchmarks 數字，不過，會放大的願景，到非常小的片段，Entity Framework 的作用。</span><span class="sxs-lookup"><span data-stu-id="eeb91-726">These microbenchmarks numbers, however, are an amplified vision into a very small fragment of what Entity Framework does.</span></span> <span data-ttu-id="eeb91-727">從 Entity Framework 5 升級至 Entity Framework 6 的暖查詢的真實世界案例不會看到效能變差。</span><span class="sxs-lookup"><span data-stu-id="eeb91-727">Real-world scenarios of warm queries should not see a performance regression when upgrading from Entity Framework 5 to Entity Framework 6.</span></span>

<span data-ttu-id="eeb91-728">若要比較不同的查詢選項的實際效能，我們會建立 5 個個別的測試變化，我們使用不同的查詢選項來選取所有的產品類別目錄名稱是 「 飲料 」。</span><span class="sxs-lookup"><span data-stu-id="eeb91-728">To compare the real-world performance of the different query options, we created 5 separate test variations where we use a different query option to select all products whose category name is "Beverages".</span></span> <span data-ttu-id="eeb91-729">每個反覆項目包含的成本建立內容，以及將所有傳回的實體具體化的成本。</span><span class="sxs-lookup"><span data-stu-id="eeb91-729">Each iteration includes the cost of creating the context, and the cost of materializing all returned entities.</span></span> <span data-ttu-id="eeb91-730">10 個反覆項目會執行 untimed 之前已逾時的 1000年次反覆運算的總和。</span><span class="sxs-lookup"><span data-stu-id="eeb91-730">10 iterations are run untimed before taking the sum of 1000 timed iterations.</span></span> <span data-ttu-id="eeb91-731">顯示的結果是取自 5 次執行每個測試的中間值執行。</span><span class="sxs-lookup"><span data-stu-id="eeb91-731">The results shown are the median run taken from 5 runs of each test.</span></span> <span data-ttu-id="eeb91-732">如需詳細資訊，請參閱附錄 B，其中包含測試的程式碼。</span><span class="sxs-lookup"><span data-stu-id="eeb91-732">For more information, see Appendix B which includes the code for the test.</span></span>

| <span data-ttu-id="eeb91-733">EF</span><span class="sxs-lookup"><span data-stu-id="eeb91-733">EF</span></span>  | <span data-ttu-id="eeb91-734">測試</span><span class="sxs-lookup"><span data-stu-id="eeb91-734">Test</span></span>                                        | <span data-ttu-id="eeb91-735">時間 （毫秒）</span><span class="sxs-lookup"><span data-stu-id="eeb91-735">Time (ms)</span></span> | <span data-ttu-id="eeb91-736">記憶體</span><span class="sxs-lookup"><span data-stu-id="eeb91-736">Memory</span></span>   |
|:----|:--------------------------------------------|:----------|:---------|
| <span data-ttu-id="eeb91-737">EF5</span><span class="sxs-lookup"><span data-stu-id="eeb91-737">EF5</span></span> | <span data-ttu-id="eeb91-738">ObjectContext 實體命令</span><span class="sxs-lookup"><span data-stu-id="eeb91-738">ObjectContext Entity Command</span></span>                | <span data-ttu-id="eeb91-739">621</span><span class="sxs-lookup"><span data-stu-id="eeb91-739">621</span></span>       | <span data-ttu-id="eeb91-740">39350272</span><span class="sxs-lookup"><span data-stu-id="eeb91-740">39350272</span></span> |
| <span data-ttu-id="eeb91-741">EF5</span><span class="sxs-lookup"><span data-stu-id="eeb91-741">EF5</span></span> | <span data-ttu-id="eeb91-742">DbContext 上資料庫的 Sql 查詢</span><span class="sxs-lookup"><span data-stu-id="eeb91-742">DbContext Sql Query on Database</span></span>             | <span data-ttu-id="eeb91-743">825</span><span class="sxs-lookup"><span data-stu-id="eeb91-743">825</span></span>       | <span data-ttu-id="eeb91-744">37519360</span><span class="sxs-lookup"><span data-stu-id="eeb91-744">37519360</span></span> |
| <span data-ttu-id="eeb91-745">EF5</span><span class="sxs-lookup"><span data-stu-id="eeb91-745">EF5</span></span> | <span data-ttu-id="eeb91-746">ObjectContext 存放區查詢</span><span class="sxs-lookup"><span data-stu-id="eeb91-746">ObjectContext Store Query</span></span>                   | <span data-ttu-id="eeb91-747">878</span><span class="sxs-lookup"><span data-stu-id="eeb91-747">878</span></span>       | <span data-ttu-id="eeb91-748">39460864</span><span class="sxs-lookup"><span data-stu-id="eeb91-748">39460864</span></span> |
| <span data-ttu-id="eeb91-749">EF5</span><span class="sxs-lookup"><span data-stu-id="eeb91-749">EF5</span></span> | <span data-ttu-id="eeb91-750">ObjectContext Linq 查詢不追蹤</span><span class="sxs-lookup"><span data-stu-id="eeb91-750">ObjectContext Linq Query No Tracking</span></span>        | <span data-ttu-id="eeb91-751">969</span><span class="sxs-lookup"><span data-stu-id="eeb91-751">969</span></span>       | <span data-ttu-id="eeb91-752">38293504</span><span class="sxs-lookup"><span data-stu-id="eeb91-752">38293504</span></span> |
| <span data-ttu-id="eeb91-753">EF5</span><span class="sxs-lookup"><span data-stu-id="eeb91-753">EF5</span></span> | <span data-ttu-id="eeb91-754">使用物件查詢的 ObjectContext Entity Sql</span><span class="sxs-lookup"><span data-stu-id="eeb91-754">ObjectContext Entity Sql using Object Query</span></span> | <span data-ttu-id="eeb91-755">1089</span><span class="sxs-lookup"><span data-stu-id="eeb91-755">1089</span></span>      | <span data-ttu-id="eeb91-756">38981632</span><span class="sxs-lookup"><span data-stu-id="eeb91-756">38981632</span></span> |
| <span data-ttu-id="eeb91-757">EF5</span><span class="sxs-lookup"><span data-stu-id="eeb91-757">EF5</span></span> | <span data-ttu-id="eeb91-758">ObjectContext 已編譯的查詢</span><span class="sxs-lookup"><span data-stu-id="eeb91-758">ObjectContext Compiled Query</span></span>                | <span data-ttu-id="eeb91-759">1099</span><span class="sxs-lookup"><span data-stu-id="eeb91-759">1099</span></span>      | <span data-ttu-id="eeb91-760">38682624</span><span class="sxs-lookup"><span data-stu-id="eeb91-760">38682624</span></span> |
| <span data-ttu-id="eeb91-761">EF5</span><span class="sxs-lookup"><span data-stu-id="eeb91-761">EF5</span></span> | <span data-ttu-id="eeb91-762">ObjectContext Linq 查詢</span><span class="sxs-lookup"><span data-stu-id="eeb91-762">ObjectContext Linq Query</span></span>                    | <span data-ttu-id="eeb91-763">1152</span><span class="sxs-lookup"><span data-stu-id="eeb91-763">1152</span></span>      | <span data-ttu-id="eeb91-764">38178816</span><span class="sxs-lookup"><span data-stu-id="eeb91-764">38178816</span></span> |
| <span data-ttu-id="eeb91-765">EF5</span><span class="sxs-lookup"><span data-stu-id="eeb91-765">EF5</span></span> | <span data-ttu-id="eeb91-766">DbContext Linq 查詢不追蹤</span><span class="sxs-lookup"><span data-stu-id="eeb91-766">DbContext Linq Query No Tracking</span></span>            | <span data-ttu-id="eeb91-767">1208</span><span class="sxs-lookup"><span data-stu-id="eeb91-767">1208</span></span>      | <span data-ttu-id="eeb91-768">41803776</span><span class="sxs-lookup"><span data-stu-id="eeb91-768">41803776</span></span> |
| <span data-ttu-id="eeb91-769">EF5</span><span class="sxs-lookup"><span data-stu-id="eeb91-769">EF5</span></span> | <span data-ttu-id="eeb91-770">DbContext DbSet 上的 Sql 查詢</span><span class="sxs-lookup"><span data-stu-id="eeb91-770">DbContext Sql Query on DbSet</span></span>                | <span data-ttu-id="eeb91-771">1414</span><span class="sxs-lookup"><span data-stu-id="eeb91-771">1414</span></span>      | <span data-ttu-id="eeb91-772">37982208</span><span class="sxs-lookup"><span data-stu-id="eeb91-772">37982208</span></span> |
| <span data-ttu-id="eeb91-773">EF5</span><span class="sxs-lookup"><span data-stu-id="eeb91-773">EF5</span></span> | <span data-ttu-id="eeb91-774">DbContext Linq 查詢</span><span class="sxs-lookup"><span data-stu-id="eeb91-774">DbContext Linq Query</span></span>                        | <span data-ttu-id="eeb91-775">1574</span><span class="sxs-lookup"><span data-stu-id="eeb91-775">1574</span></span>      | <span data-ttu-id="eeb91-776">41738240</span><span class="sxs-lookup"><span data-stu-id="eeb91-776">41738240</span></span> |
|     |                                             |           |          |
| <span data-ttu-id="eeb91-777">EF6</span><span class="sxs-lookup"><span data-stu-id="eeb91-777">EF6</span></span> | <span data-ttu-id="eeb91-778">ObjectContext 實體命令</span><span class="sxs-lookup"><span data-stu-id="eeb91-778">ObjectContext Entity Command</span></span>                | <span data-ttu-id="eeb91-779">480</span><span class="sxs-lookup"><span data-stu-id="eeb91-779">480</span></span>       | <span data-ttu-id="eeb91-780">47247360</span><span class="sxs-lookup"><span data-stu-id="eeb91-780">47247360</span></span> |
| <span data-ttu-id="eeb91-781">EF6</span><span class="sxs-lookup"><span data-stu-id="eeb91-781">EF6</span></span> | <span data-ttu-id="eeb91-782">ObjectContext 存放區查詢</span><span class="sxs-lookup"><span data-stu-id="eeb91-782">ObjectContext Store Query</span></span>                   | <span data-ttu-id="eeb91-783">493</span><span class="sxs-lookup"><span data-stu-id="eeb91-783">493</span></span>       | <span data-ttu-id="eeb91-784">46739456</span><span class="sxs-lookup"><span data-stu-id="eeb91-784">46739456</span></span> |
| <span data-ttu-id="eeb91-785">EF6</span><span class="sxs-lookup"><span data-stu-id="eeb91-785">EF6</span></span> | <span data-ttu-id="eeb91-786">DbContext 上資料庫的 Sql 查詢</span><span class="sxs-lookup"><span data-stu-id="eeb91-786">DbContext Sql Query on Database</span></span>             | <span data-ttu-id="eeb91-787">614</span><span class="sxs-lookup"><span data-stu-id="eeb91-787">614</span></span>       | <span data-ttu-id="eeb91-788">41607168</span><span class="sxs-lookup"><span data-stu-id="eeb91-788">41607168</span></span> |
| <span data-ttu-id="eeb91-789">EF6</span><span class="sxs-lookup"><span data-stu-id="eeb91-789">EF6</span></span> | <span data-ttu-id="eeb91-790">ObjectContext Linq 查詢不追蹤</span><span class="sxs-lookup"><span data-stu-id="eeb91-790">ObjectContext Linq Query No Tracking</span></span>        | <span data-ttu-id="eeb91-791">684</span><span class="sxs-lookup"><span data-stu-id="eeb91-791">684</span></span>       | <span data-ttu-id="eeb91-792">46333952</span><span class="sxs-lookup"><span data-stu-id="eeb91-792">46333952</span></span> |
| <span data-ttu-id="eeb91-793">EF6</span><span class="sxs-lookup"><span data-stu-id="eeb91-793">EF6</span></span> | <span data-ttu-id="eeb91-794">使用物件查詢的 ObjectContext Entity Sql</span><span class="sxs-lookup"><span data-stu-id="eeb91-794">ObjectContext Entity Sql using Object Query</span></span> | <span data-ttu-id="eeb91-795">767</span><span class="sxs-lookup"><span data-stu-id="eeb91-795">767</span></span>       | <span data-ttu-id="eeb91-796">48865280</span><span class="sxs-lookup"><span data-stu-id="eeb91-796">48865280</span></span> |
| <span data-ttu-id="eeb91-797">EF6</span><span class="sxs-lookup"><span data-stu-id="eeb91-797">EF6</span></span> | <span data-ttu-id="eeb91-798">ObjectContext 已編譯的查詢</span><span class="sxs-lookup"><span data-stu-id="eeb91-798">ObjectContext Compiled Query</span></span>                | <span data-ttu-id="eeb91-799">788</span><span class="sxs-lookup"><span data-stu-id="eeb91-799">788</span></span>       | <span data-ttu-id="eeb91-800">48467968</span><span class="sxs-lookup"><span data-stu-id="eeb91-800">48467968</span></span> |
| <span data-ttu-id="eeb91-801">EF6</span><span class="sxs-lookup"><span data-stu-id="eeb91-801">EF6</span></span> | <span data-ttu-id="eeb91-802">DbContext Linq 查詢不追蹤</span><span class="sxs-lookup"><span data-stu-id="eeb91-802">DbContext Linq Query No Tracking</span></span>            | <span data-ttu-id="eeb91-803">878</span><span class="sxs-lookup"><span data-stu-id="eeb91-803">878</span></span>       | <span data-ttu-id="eeb91-804">47554560</span><span class="sxs-lookup"><span data-stu-id="eeb91-804">47554560</span></span> |
| <span data-ttu-id="eeb91-805">EF6</span><span class="sxs-lookup"><span data-stu-id="eeb91-805">EF6</span></span> | <span data-ttu-id="eeb91-806">ObjectContext Linq 查詢</span><span class="sxs-lookup"><span data-stu-id="eeb91-806">ObjectContext Linq Query</span></span>                    | <span data-ttu-id="eeb91-807">953</span><span class="sxs-lookup"><span data-stu-id="eeb91-807">953</span></span>       | <span data-ttu-id="eeb91-808">47632384</span><span class="sxs-lookup"><span data-stu-id="eeb91-808">47632384</span></span> |
| <span data-ttu-id="eeb91-809">EF6</span><span class="sxs-lookup"><span data-stu-id="eeb91-809">EF6</span></span> | <span data-ttu-id="eeb91-810">DbContext DbSet 上的 Sql 查詢</span><span class="sxs-lookup"><span data-stu-id="eeb91-810">DbContext Sql Query on DbSet</span></span>                | <span data-ttu-id="eeb91-811">1023</span><span class="sxs-lookup"><span data-stu-id="eeb91-811">1023</span></span>      | <span data-ttu-id="eeb91-812">41992192</span><span class="sxs-lookup"><span data-stu-id="eeb91-812">41992192</span></span> |
| <span data-ttu-id="eeb91-813">EF6</span><span class="sxs-lookup"><span data-stu-id="eeb91-813">EF6</span></span> | <span data-ttu-id="eeb91-814">DbContext Linq 查詢</span><span class="sxs-lookup"><span data-stu-id="eeb91-814">DbContext Linq Query</span></span>                        | <span data-ttu-id="eeb91-815">1290</span><span class="sxs-lookup"><span data-stu-id="eeb91-815">1290</span></span>      | <span data-ttu-id="eeb91-816">47529984</span><span class="sxs-lookup"><span data-stu-id="eeb91-816">47529984</span></span> |


![EF5 暖查詢 1000年反覆項目](~/ef6/media/ef5warmquery1000.png)

![EF6 暖查詢 1000年反覆項目](~/ef6/media/ef6warmquery1000.png)

> [!NOTE]
> <span data-ttu-id="eeb91-819">為求完整性，我們加入我們在 EntityCommand 執行 Entity SQL 查詢的其中一種變化。</span><span class="sxs-lookup"><span data-stu-id="eeb91-819">For completeness, we included a variation where we execute an Entity SQL query on an EntityCommand.</span></span> <span data-ttu-id="eeb91-820">不過，因為結果不會針對這類查詢具體化，比較不一定是蘋果對蘋果。</span><span class="sxs-lookup"><span data-stu-id="eeb91-820">However, because results are not materialized for such queries, the comparison isn't necessarily apples-to-apples.</span></span> <span data-ttu-id="eeb91-821">測試包含最接近的近似值具體化嘗試進行了比較。</span><span class="sxs-lookup"><span data-stu-id="eeb91-821">The test includes a close approximation to materializing to try making the comparison fairer.</span></span>

<span data-ttu-id="eeb91-822">在此端對端案例中，Entity Framework 6，其表現優於 Entity Framework 5 因為對堆疊，包括更輕的 DbContext 初始化和更快速的 MetadataCollection 的幾個部分的效能改進&lt;T&gt;查閱。</span><span class="sxs-lookup"><span data-stu-id="eeb91-822">In this end-to-end case, Entity Framework 6 outperforms Entity Framework 5 due to performance improvements made on several parts of the stack, including a much lighter DbContext initialization and faster MetadataCollection&lt;T&gt; lookups.</span></span>

## <a name="7-design-time-performance-considerations"></a><span data-ttu-id="eeb91-823">7 設計時間效能考量</span><span class="sxs-lookup"><span data-stu-id="eeb91-823">7 Design time performance considerations</span></span>

### <a name="71-------inheritance-strategies"></a><span data-ttu-id="eeb91-824">7.1 繼承策略</span><span class="sxs-lookup"><span data-stu-id="eeb91-824">7.1       Inheritance Strategies</span></span>

<span data-ttu-id="eeb91-825">另一個使用 Entity Framework 時的效能考量是您所使用的繼承策略。</span><span class="sxs-lookup"><span data-stu-id="eeb91-825">Another performance consideration when using Entity Framework is the inheritance strategy you use.</span></span> <span data-ttu-id="eeb91-826">Entity Framework 支援 3 個基本類型的繼承和其組合：</span><span class="sxs-lookup"><span data-stu-id="eeb91-826">Entity Framework supports 3 basic types of inheritance and their combinations:</span></span>

-   <span data-ttu-id="eeb91-827">每個階層 (TPH) – 每個繼承用來設定具有一個鑑別子資料行指出在階層中的特定型別所表示的資料列中的資料表對應的資料表。</span><span class="sxs-lookup"><span data-stu-id="eeb91-827">Table per Hierarchy (TPH) – where each inheritance set maps to a table with a discriminator column to indicate which particular type in the hierarchy is being represented in the row.</span></span>
-   <span data-ttu-id="eeb91-828">每個型別 (TPT) – 其中每個類型會有自己的資料表在資料庫資料表子資料表只會定義父資料表不包含的資料行。</span><span class="sxs-lookup"><span data-stu-id="eeb91-828">Table per Type (TPT) – where each type has its own table in the database; the child tables only define the columns that the parent table doesn’t contain.</span></span>
-   <span data-ttu-id="eeb91-829">每個類別 (TPC) – 每一種類型其中有它自己的完整資料表在資料庫資料表子資料表定義他們的所有欄位，包括父型別中定義的。</span><span class="sxs-lookup"><span data-stu-id="eeb91-829">Table per Class (TPC) – where each type has its own full table in the database; the child tables define all their fields, including those defined in parent types.</span></span>

<span data-ttu-id="eeb91-830">如果您的模型使用 TPT 繼承，產生的查詢將會比使用其他繼承策略，這可能會導致儲存區上的執行時間較長上產生的更為複雜。</span><span class="sxs-lookup"><span data-stu-id="eeb91-830">If your model uses TPT inheritance, the queries which are generated will be more complex than those that are generated with the other inheritance strategies, which may result on longer execution times on the store.</span></span>  <span data-ttu-id="eeb91-831">它通常會比較長 TPT 模型，產生查詢，具體化結果的物件。</span><span class="sxs-lookup"><span data-stu-id="eeb91-831">It will generally take longer to generate queries over a TPT model, and to materialize the resulting objects.</span></span>

<span data-ttu-id="eeb91-832">請參閱的 < 效能考量當 Entity Framework 中使用 （每個類型的資料表） TPT 繼承 > MSDN 部落格文章： \< http://blogs.msdn.com/b/adonet/archive/2010/08/17/performance-considerations-when-using-tpt-table-per-type-inheritance-in-the-entity-framework.aspx>。</span><span class="sxs-lookup"><span data-stu-id="eeb91-832">See the "Performance Considerations when using TPT (Table per Type) Inheritance in the Entity Framework" MSDN blog post: \<http://blogs.msdn.com/b/adonet/archive/2010/08/17/performance-considerations-when-using-tpt-table-per-type-inheritance-in-the-entity-framework.aspx>.</span></span>

#### <a name="711-------avoiding-tpt-in-model-first-or-code-first-applications"></a><span data-ttu-id="eeb91-833">7.1.1 Model First 或 Code First 應用程式中避免 TPT</span><span class="sxs-lookup"><span data-stu-id="eeb91-833">7.1.1       Avoiding TPT in Model First or Code First applications</span></span>

<span data-ttu-id="eeb91-834">當您建立模型以覆蓋現有資料庫具有 TPT 結構描述時，您不需要許多選項。</span><span class="sxs-lookup"><span data-stu-id="eeb91-834">When you create a model over an existing database that has a TPT schema, you don't have many options.</span></span> <span data-ttu-id="eeb91-835">但在建立時使用 Model First 或 Code First 應用程式，您應該避免 TPT 繼承，基於效能考量。</span><span class="sxs-lookup"><span data-stu-id="eeb91-835">But when creating an application using Model First or Code First, you should avoid TPT inheritance for performance concerns.</span></span>

<span data-ttu-id="eeb91-836">當您使用第一個模型的實體設計工具精靈 中時，您會收到的任何繼承的 TPT 模型中。</span><span class="sxs-lookup"><span data-stu-id="eeb91-836">When you use Model First in the Entity Designer Wizard, you will get TPT for any inheritance in your model.</span></span> <span data-ttu-id="eeb91-837">如果您想要切換成使用 Model First TPH 繼承策略，您可以使用 「 實體設計工具資料庫產生 Power Pack 」 可從 Visual Studio 組件庫 ( \< http://visualstudiogallery.msdn.microsoft.com/df3541c3-d833-4b65-b942-989e7ec74c87/>)。</span><span class="sxs-lookup"><span data-stu-id="eeb91-837">If you want to switch to a TPH inheritance strategy with Model First, you can use the "Entity Designer Database Generation Power Pack" available from the Visual Studio Gallery ( \<http://visualstudiogallery.msdn.microsoft.com/df3541c3-d833-4b65-b942-989e7ec74c87/>).</span></span>

<span data-ttu-id="eeb91-838">當使用 Code First 來設定模型的對應具有繼承，EF 會使用預設的 TPH，因此繼承階層架構中的所有實體將會都對應到相同的資料表。</span><span class="sxs-lookup"><span data-stu-id="eeb91-838">When using Code First to configure the mapping of a model with inheritance, EF will use TPH by default, therefore all entities in the inheritance hierarchy will be mapped to the same table.</span></span> <span data-ttu-id="eeb91-839">請參閱 MSDN Magazine 中的 「 程式碼第一次在實體 Framework4.1"發行項"對應使用 Fluent API 」 一節 ( [ http://msdn.microsoft.com/magazine/hh126815.aspx ](https://msdn.microsoft.com/magazine/hh126815.aspx)) 如需詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="eeb91-839">See the "Mapping with the Fluent API" section of the "Code First in Entity Framework4.1" article in MSDN Magazine ( [http://msdn.microsoft.com/magazine/hh126815.aspx](https://msdn.microsoft.com/magazine/hh126815.aspx)) for more details.</span></span>

### <a name="72-------upgrading-from-ef4-to-improve-model-generation-time"></a><span data-ttu-id="eeb91-840">7.2 升級從 EF4 來改善模型的產生時間</span><span class="sxs-lookup"><span data-stu-id="eeb91-840">7.2       Upgrading from EF4 to improve model generation time</span></span>

<span data-ttu-id="eeb91-841">安裝 Visual Studio 2010 SP1 時，Entity Framework 5 和 6，及做為 Entity Framework 4 的更新可用的模型產生存放區層 (SSDL) 的演算法，SQL Server 特定改進。</span><span class="sxs-lookup"><span data-stu-id="eeb91-841">A SQL Server-specific improvement to the algorithm that generates the store-layer (SSDL) of the model is available in Entity Framework 5 and 6, and as an update to Entity Framework 4 when Visual Studio 2010 SP1 is installed.</span></span> <span data-ttu-id="eeb91-842">下列的測試結果時產生非常大的模型中，在此情況下 Navision 模型，示範改進。</span><span class="sxs-lookup"><span data-stu-id="eeb91-842">The following test results demonstrate the improvement when generating a very big model, in this case the Navision model.</span></span> <span data-ttu-id="eeb91-843">如需進一步瞭解羰旓紵 C。</span><span class="sxs-lookup"><span data-stu-id="eeb91-843">See Appendix C for more details about it.</span></span>

<span data-ttu-id="eeb91-844">模型包含 1005年實體集和 4227 關聯集。</span><span class="sxs-lookup"><span data-stu-id="eeb91-844">The model contains 1005 entity sets and 4227 association sets.</span></span>

| <span data-ttu-id="eeb91-845">組態</span><span class="sxs-lookup"><span data-stu-id="eeb91-845">Configuration</span></span>                              | <span data-ttu-id="eeb91-846">耗用時間的細項</span><span class="sxs-lookup"><span data-stu-id="eeb91-846">Breakdown of time consumed</span></span>                                                                                                                                               |
|:-------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="eeb91-847">Visual Studio 2010 中，Entity Framework 4</span><span class="sxs-lookup"><span data-stu-id="eeb91-847">Visual Studio 2010, Entity Framework 4</span></span>     | <span data-ttu-id="eeb91-848">SSDL 產生： 2 小時 27 分鐘</span><span class="sxs-lookup"><span data-stu-id="eeb91-848">SSDL Generation: 2 hr 27 min</span></span> <br/> <span data-ttu-id="eeb91-849">對應層代： 1 秒</span><span class="sxs-lookup"><span data-stu-id="eeb91-849">Mapping Generation: 1 second</span></span> <br/> <span data-ttu-id="eeb91-850">CSDL 產生： 1 秒</span><span class="sxs-lookup"><span data-stu-id="eeb91-850">CSDL Generation: 1 second</span></span> <br/> <span data-ttu-id="eeb91-851">ObjectLayer 產生： 1 秒</span><span class="sxs-lookup"><span data-stu-id="eeb91-851">ObjectLayer Generation: 1 second</span></span> <br/> <span data-ttu-id="eeb91-852">檢視表產生： 2 小時 14 分鐘</span><span class="sxs-lookup"><span data-stu-id="eeb91-852">View Generation: 2 h 14 min</span></span> |
| <span data-ttu-id="eeb91-853">Visual Studio 2010 SP1 中，Entity Framework 4</span><span class="sxs-lookup"><span data-stu-id="eeb91-853">Visual Studio 2010 SP1, Entity Framework 4</span></span> | <span data-ttu-id="eeb91-854">SSDL 產生： 1 秒</span><span class="sxs-lookup"><span data-stu-id="eeb91-854">SSDL Generation: 1 second</span></span> <br/> <span data-ttu-id="eeb91-855">對應層代： 1 秒</span><span class="sxs-lookup"><span data-stu-id="eeb91-855">Mapping Generation: 1 second</span></span> <br/> <span data-ttu-id="eeb91-856">CSDL 產生： 1 秒</span><span class="sxs-lookup"><span data-stu-id="eeb91-856">CSDL Generation: 1 second</span></span> <br/> <span data-ttu-id="eeb91-857">ObjectLayer 產生： 1 秒</span><span class="sxs-lookup"><span data-stu-id="eeb91-857">ObjectLayer Generation: 1 second</span></span> <br/> <span data-ttu-id="eeb91-858">檢視表產生： 1 小時 53 分鐘</span><span class="sxs-lookup"><span data-stu-id="eeb91-858">View Generation: 1 hr 53 min</span></span>   |
| <span data-ttu-id="eeb91-859">Visual Studio 2013 中，Entity Framework 5</span><span class="sxs-lookup"><span data-stu-id="eeb91-859">Visual Studio 2013, Entity Framework 5</span></span>     | <span data-ttu-id="eeb91-860">SSDL 產生： 1 秒</span><span class="sxs-lookup"><span data-stu-id="eeb91-860">SSDL Generation: 1 second</span></span> <br/> <span data-ttu-id="eeb91-861">對應層代： 1 秒</span><span class="sxs-lookup"><span data-stu-id="eeb91-861">Mapping Generation: 1 second</span></span> <br/> <span data-ttu-id="eeb91-862">CSDL 產生： 1 秒</span><span class="sxs-lookup"><span data-stu-id="eeb91-862">CSDL Generation: 1 second</span></span> <br/> <span data-ttu-id="eeb91-863">ObjectLayer 產生： 1 秒</span><span class="sxs-lookup"><span data-stu-id="eeb91-863">ObjectLayer Generation: 1 second</span></span> <br/> <span data-ttu-id="eeb91-864">檢視表產生： 65 分鐘</span><span class="sxs-lookup"><span data-stu-id="eeb91-864">View Generation: 65 minutes</span></span>    |
| <span data-ttu-id="eeb91-865">Visual Studio 2013 中，Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="eeb91-865">Visual Studio 2013, Entity Framework 6</span></span>     | <span data-ttu-id="eeb91-866">SSDL 產生： 1 秒</span><span class="sxs-lookup"><span data-stu-id="eeb91-866">SSDL Generation: 1 second</span></span> <br/> <span data-ttu-id="eeb91-867">對應層代： 1 秒</span><span class="sxs-lookup"><span data-stu-id="eeb91-867">Mapping Generation: 1 second</span></span> <br/> <span data-ttu-id="eeb91-868">CSDL 產生： 1 秒</span><span class="sxs-lookup"><span data-stu-id="eeb91-868">CSDL Generation: 1 second</span></span> <br/> <span data-ttu-id="eeb91-869">ObjectLayer 產生： 1 秒</span><span class="sxs-lookup"><span data-stu-id="eeb91-869">ObjectLayer Generation: 1 second</span></span> <br/> <span data-ttu-id="eeb91-870">檢視表產生： 28 秒。</span><span class="sxs-lookup"><span data-stu-id="eeb91-870">View Generation: 28 seconds.</span></span>   |


<span data-ttu-id="eeb91-871">值得注意的是，當產生的 SSDL，負載幾乎完全花費在 SQL 伺服器上，等候用戶端開發電腦時閒置來自伺服器的結果。</span><span class="sxs-lookup"><span data-stu-id="eeb91-871">It's worth noting that when generating the SSDL, the load is almost entirely spent on the SQL Server, while the client development machine is waiting idle for results to come back from the server.</span></span> <span data-ttu-id="eeb91-872">Dba 應該特別喜歡這項改善。</span><span class="sxs-lookup"><span data-stu-id="eeb91-872">DBAs should particularly appreciate this improvement.</span></span> <span data-ttu-id="eeb91-873">另外值得注意的是基本上模型產生的全部費用現在會在檢視表產生。</span><span class="sxs-lookup"><span data-stu-id="eeb91-873">It's also worth noting that essentially the entire cost of model generation takes place in View Generation now.</span></span>

### <a name="73-------splitting-large-models-with-database-first-and-model-first"></a><span data-ttu-id="eeb91-874">7.3 先分割大型模型與資料庫和 Model First</span><span class="sxs-lookup"><span data-stu-id="eeb91-874">7.3       Splitting Large Models with Database First and Model First</span></span>

<span data-ttu-id="eeb91-875">模型大小增加時，在設計工具介面變得雜亂且難以使用。</span><span class="sxs-lookup"><span data-stu-id="eeb91-875">As model size increases, the designer surface becomes cluttered and difficult to use.</span></span> <span data-ttu-id="eeb91-876">我們通常會視為具有 300 個以上的實體太大，無法有效地使用設計工具的模型。</span><span class="sxs-lookup"><span data-stu-id="eeb91-876">We typically consider a model with more than 300 entities to be too large to effectively use the designer.</span></span> <span data-ttu-id="eeb91-877">下列部落格文章說明用來分割大型模型的數個選項： \< http://blogs.msdn.com/b/adonet/archive/2008/11/25/working-with-large-models-in-entity-framework-part-2.aspx>。</span><span class="sxs-lookup"><span data-stu-id="eeb91-877">The following blog post describes several options for splitting large models: \<http://blogs.msdn.com/b/adonet/archive/2008/11/25/working-with-large-models-in-entity-framework-part-2.aspx>.</span></span>

<span data-ttu-id="eeb91-878">文章針對 Entity Framework 的第一個版本所撰寫，但步驟仍然適用。</span><span class="sxs-lookup"><span data-stu-id="eeb91-878">The post was written for the first version of Entity Framework, but the steps still apply.</span></span>

### <a name="74-------performance-considerations-with-the-entity-data-source-control"></a><span data-ttu-id="eeb91-879">7.4 實體資料來源控制項的效能考量</span><span class="sxs-lookup"><span data-stu-id="eeb91-879">7.4       Performance considerations with the Entity Data Source Control</span></span>

<span data-ttu-id="eeb91-880">我們已了解在多執行緒的效能和壓力測試的情況下使用 EntityDataSource 控制項的 web 應用程式的效能大幅降低的位置。</span><span class="sxs-lookup"><span data-stu-id="eeb91-880">We've seen cases in multi-threaded performance and stress tests where the performance of a web application using the EntityDataSource Control deteriorates significantly.</span></span> <span data-ttu-id="eeb91-881">根本原因是，EntityDataSource 重複 MetadataWorkspace.LoadFromAssembly 上呼叫的 Web 應用程式，以探索要做為實體型別所參考的組件。</span><span class="sxs-lookup"><span data-stu-id="eeb91-881">The underlying cause is that the EntityDataSource repeatedly calls MetadataWorkspace.LoadFromAssembly on the assemblies referenced by the Web application to discover the types to be used as entities.</span></span>

<span data-ttu-id="eeb91-882">解決方法是設定的 EntityDataSource ContextTypeName 衍生的 ObjectContext 類別的型別名稱。</span><span class="sxs-lookup"><span data-stu-id="eeb91-882">The solution is to set the ContextTypeName of the EntityDataSource to the type name of your derived ObjectContext class.</span></span> <span data-ttu-id="eeb91-883">這會關閉掃描的實體類型的所有參考組件的機制。</span><span class="sxs-lookup"><span data-stu-id="eeb91-883">This turns off the mechanism that scans all referenced assemblies for entity types.</span></span>

<span data-ttu-id="eeb91-884">將 ContextTypeName 欄位時，也可防止功能性的問題，其中在.NET 4.0 中的 EntityDataSource 時擲回 ReflectionTypeLoadException 它無法透過反映的組件載入的類型。</span><span class="sxs-lookup"><span data-stu-id="eeb91-884">Setting the ContextTypeName field also prevents a functional problem where the EntityDataSource in .NET 4.0 throws a ReflectionTypeLoadException when it can't load a type from an assembly via reflection.</span></span> <span data-ttu-id="eeb91-885">在.NET 4.5 中已修正此問題。</span><span class="sxs-lookup"><span data-stu-id="eeb91-885">This issue has been fixed in .NET 4.5.</span></span>

### <a name="75-------poco-entities-and-change-tracking-proxies"></a><span data-ttu-id="eeb91-886">7.5 的 POCO 實體和變更追蹤 proxy</span><span class="sxs-lookup"><span data-stu-id="eeb91-886">7.5       POCO entities and change tracking proxies</span></span>

<span data-ttu-id="eeb91-887">Entity Framework 可讓您使用自訂資料類別加上您的資料模型，而不需要對資料類別本身進行任何修改。</span><span class="sxs-lookup"><span data-stu-id="eeb91-887">Entity Framework enables you to use custom data classes together with your data model without making any modifications to the data classes themselves.</span></span> <span data-ttu-id="eeb91-888">這表示您可以使用「單純」(plain-old) CLR 物件 (POCO)，例如現有的網域物件，加上您的資料模型。</span><span class="sxs-lookup"><span data-stu-id="eeb91-888">This means that you can use "plain-old" CLR objects (POCO), such as existing domain objects, with your data model.</span></span> <span data-ttu-id="eeb91-889">這些 POCO 資料類別 （也稱為非持續物件），這會對應到資料模型中定義的實體，支援的大部分相同的查詢、 插入、 更新和刪除行為 Entity Data Model 工具所產生的實體類型。</span><span class="sxs-lookup"><span data-stu-id="eeb91-889">These POCO data classes (also known as persistence-ignorant objects), which are mapped to entities that are defined in a data model, support most of the same query, insert, update, and delete behaviors as entity types that are generated by the Entity Data Model tools.</span></span>

<span data-ttu-id="eeb91-890">Entity Framework 也可以建立衍生自您的 POCO 類型，您想要啟用功能，例如消極式載入和自動變更追蹤 POCO 實體時所使用的 proxy 類別。</span><span class="sxs-lookup"><span data-stu-id="eeb91-890">Entity Framework can also create proxy classes derived from your POCO types, which are used when you want to enable features such as lazy loading and automatic change tracking on POCO entities.</span></span> <span data-ttu-id="eeb91-891">您的 POCO 類別必須符合特定需求，才能讓 Entity Framework，才可使用 proxy，如下所述： [ http://msdn.microsoft.com/library/dd468057.aspx ](https://msdn.microsoft.com/library/dd468057.aspx)。</span><span class="sxs-lookup"><span data-stu-id="eeb91-891">Your POCO classes must meet certain requirements to allow Entity Framework to use proxies, as described here: [http://msdn.microsoft.com/library/dd468057.aspx](https://msdn.microsoft.com/library/dd468057.aspx).</span></span>

<span data-ttu-id="eeb91-892">機會追蹤 proxy 會通知物件狀態管理員每任何的次實體的屬性有變更，其值，讓 Entity Framework 知道您的實體的實際狀態的時間。</span><span class="sxs-lookup"><span data-stu-id="eeb91-892">Chance tracking proxies will notify the object state manager each time any of the properties of your entities has its value changed, so Entity Framework knows the actual state of your entities all the time.</span></span> <span data-ttu-id="eeb91-893">這是藉由將通知事件加入至您的屬性 setter 方法的主體並處理這類事件的物件狀態管理員。</span><span class="sxs-lookup"><span data-stu-id="eeb91-893">This is done by adding notification events to the body of the setter methods of your properties, and having the object state manager processing such events.</span></span> <span data-ttu-id="eeb91-894">請注意，建立 proxy 實體通常會是成本高於建立非 proxy 的 POCO 實體，因為已新增的 Entity Framework 所建立的事件集。</span><span class="sxs-lookup"><span data-stu-id="eeb91-894">Note that creating a proxy entity will typically be more expensive than creating a non-proxy POCO entity due to the added set of events created by Entity Framework.</span></span>

<span data-ttu-id="eeb91-895">當 POCO 實體並沒有變更追蹤 proxy 時，藉由比較您針對先前已儲存狀態的複本的實體的內容中發現有所變更。</span><span class="sxs-lookup"><span data-stu-id="eeb91-895">When a POCO entity does not have a change tracking proxy, changes are found by comparing the contents of your entities against a copy of a previous saved state.</span></span> <span data-ttu-id="eeb91-896">這個深度的比較會變成耗時的程序中您的內容，有許多實體時，或當實體有非常大量的屬性，即使沒有任何變更後的最後一個比較發生。</span><span class="sxs-lookup"><span data-stu-id="eeb91-896">This deep comparison will become a lengthy process when you have many entities in your context, or when your entities have a very large amount of properties, even if none of them changed since the last comparison took place.</span></span>

<span data-ttu-id="eeb91-897">摘要： 您將需支付對效能造成影響時建立的變更追蹤 proxy，但變更追蹤可協助您加速變更偵測程序，當您的實體有許多屬性，或當您在模型中有許多實體。</span><span class="sxs-lookup"><span data-stu-id="eeb91-897">In summary: you’ll pay a performance hit when creating the change tracking proxy, but change tracking will help you speed up the change detection process when your entities have many properties or when you have many entities in your model.</span></span> <span data-ttu-id="eeb91-898">對於具有少數屬性所在的實體數量不會太多的實體，具有變更追蹤 proxy 不可能的很多優點。</span><span class="sxs-lookup"><span data-stu-id="eeb91-898">For entities with a small number of properties where the amount of entities doesn’t grow too much, having change tracking proxies may not be of much benefit.</span></span>

## <a name="8-loading-related-entities"></a><span data-ttu-id="eeb91-899">8 載入相關實體</span><span class="sxs-lookup"><span data-stu-id="eeb91-899">8 Loading Related Entities</span></span>

### <a name="81-lazy-loading-vs-eager-loading"></a><span data-ttu-id="eeb91-900">8.1 消極式載入 vs。積極式載入</span><span class="sxs-lookup"><span data-stu-id="eeb91-900">8.1 Lazy Loading vs. Eager Loading</span></span>

<span data-ttu-id="eeb91-901">Entity Framework 提供數個不同的方式，將會關聯至您的目標實體的實體。</span><span class="sxs-lookup"><span data-stu-id="eeb91-901">Entity Framework offers several different ways to load the entities that are related to your target entity.</span></span> <span data-ttu-id="eeb91-902">例如，當您查詢產品時，有許多種相關的訂單將會載入到物件狀態管理員。</span><span class="sxs-lookup"><span data-stu-id="eeb91-902">For example, when you query for Products, there are different ways that the related Orders will be loaded into the Object State Manager.</span></span> <span data-ttu-id="eeb91-903">從效能觀點來看，來載入相關的實體時，請考慮將最大問題，也將會使用消極式載入，或積極式載入。</span><span class="sxs-lookup"><span data-stu-id="eeb91-903">From a performance standpoint, the biggest question to consider when loading related entities will be whether to use Lazy Loading or Eager Loading.</span></span>

<span data-ttu-id="eeb91-904">當使用積極式載入，以及您的目標實體集載入相關的實體。</span><span class="sxs-lookup"><span data-stu-id="eeb91-904">When using Eager Loading, the related entities are loaded along with your target entity set.</span></span> <span data-ttu-id="eeb91-905">您可以在查詢中使用 Include 陳述式，以指出哪些相關的實體，您想要納入。</span><span class="sxs-lookup"><span data-stu-id="eeb91-905">You use an Include statement in your query to indicate which related entities you want to bring in.</span></span>

<span data-ttu-id="eeb91-906">使用消極式載入，當您最初的查詢只會顯示目標實體集裡。</span><span class="sxs-lookup"><span data-stu-id="eeb91-906">When using Lazy Loading, your initial query only brings in the target entity set.</span></span> <span data-ttu-id="eeb91-907">但每當存取導覽屬性時，另一個查詢會發出對存放區載入相關的實體。</span><span class="sxs-lookup"><span data-stu-id="eeb91-907">But whenever you access a navigation property, another query is issued against the store to load the related entity.</span></span>

<span data-ttu-id="eeb91-908">實體載入之後，實體的任何進一步的查詢會將其載入直接從物件狀態管理員中，不論您使用消極式載入 」 或 「 積極式載入。</span><span class="sxs-lookup"><span data-stu-id="eeb91-908">Once an entity has been loaded, any further queries for the entity will load it directly from the Object State Manager, whether you are using lazy loading or eager loading.</span></span>

### <a name="82-how-to-choose-between-lazy-loading-and-eager-loading"></a><span data-ttu-id="eeb91-909">8.2 如何選擇消極式載入，並積極式載入</span><span class="sxs-lookup"><span data-stu-id="eeb91-909">8.2 How to choose between Lazy Loading and Eager Loading</span></span>

<span data-ttu-id="eeb91-910">重點是，讓您可以讓您的應用程式中的正確的選擇，了解消極式載入，並積極式載入的差異。</span><span class="sxs-lookup"><span data-stu-id="eeb91-910">The important thing is that you understand the difference between Lazy Loading and Eager Loading so that you can make the correct choice for your application.</span></span> <span data-ttu-id="eeb91-911">這將協助您評估對資料庫與單一要求可能包含大型承載的多個要求之間的權衡取捨。</span><span class="sxs-lookup"><span data-stu-id="eeb91-911">This will help you evaluate the tradeoff between multiple requests against the database versus a single request that may contain a large payload.</span></span> <span data-ttu-id="eeb91-912">它可能適用於在您的應用程式的某些部分的積極式載入和消極式載入其他組件中。</span><span class="sxs-lookup"><span data-stu-id="eeb91-912">It may be appropriate to use eager loading in some parts of your application and lazy loading in other parts.</span></span>

<span data-ttu-id="eeb91-913">例如在幕後發生了什麼，假設您想要住在英國和其訂單計數的客戶查詢。</span><span class="sxs-lookup"><span data-stu-id="eeb91-913">As an example of what's happening under the hood, suppose you want to query for the customers who live in the UK and their order count.</span></span>

<span data-ttu-id="eeb91-914">**使用積極式載入**</span><span class="sxs-lookup"><span data-stu-id="eeb91-914">**Using Eager Loading**</span></span>

``` csharp
using (NorthwindEntities context = new NorthwindEntities())
{
    var ukCustomers = context.Customers.Include(c => c.Orders).Where(c => c.Address.Country == "UK");
    var chosenCustomer = AskUserToPickCustomer(ukCustomers);
    Console.WriteLine("Customer Id: {0} has {1} orders", customer.CustomerID, customer.Orders.Count);
}
```

<span data-ttu-id="eeb91-915">**使用消極式載入**</span><span class="sxs-lookup"><span data-stu-id="eeb91-915">**Using Lazy Loading**</span></span>

``` csharp
using (NorthwindEntities context = new NorthwindEntities())
{
    context.ContextOptions.LazyLoadingEnabled = true;

    //Notice that the Include method call is missing in the query
    var ukCustomers = context.Customers.Where(c => c.Address.Country == "UK");

    var chosenCustomer = AskUserToPickCustomer(ukCustomers);
    Console.WriteLine("Customer Id: {0} has {1} orders", customer.CustomerID, customer.Orders.Count);
}
```

<span data-ttu-id="eeb91-916">當使用積極式載入，您將會發出單一查詢，傳回所有客戶] 和 [所有的訂單。</span><span class="sxs-lookup"><span data-stu-id="eeb91-916">When using eager loading, you'll issue a single query that returns all customers and all orders.</span></span> <span data-ttu-id="eeb91-917">存放區命令看起來像：</span><span class="sxs-lookup"><span data-stu-id="eeb91-917">The store command looks like:</span></span>

``` SQL
SELECT
[Project1].[C1] AS [C1],
[Project1].[CustomerID] AS [CustomerID],
[Project1].[CompanyName] AS [CompanyName],
[Project1].[ContactName] AS [ContactName],
[Project1].[ContactTitle] AS [ContactTitle],
[Project1].[Address] AS [Address],
[Project1].[City] AS [City],
[Project1].[Region] AS [Region],
[Project1].[PostalCode] AS [PostalCode],
[Project1].[Country] AS [Country],
[Project1].[Phone] AS [Phone],
[Project1].[Fax] AS [Fax],
[Project1].[C2] AS [C2],
[Project1].[OrderID] AS [OrderID],
[Project1].[CustomerID1] AS [CustomerID1],
[Project1].[EmployeeID] AS [EmployeeID],
[Project1].[OrderDate] AS [OrderDate],
[Project1].[RequiredDate] AS [RequiredDate],
[Project1].[ShippedDate] AS [ShippedDate],
[Project1].[ShipVia] AS [ShipVia],
[Project1].[Freight] AS [Freight],
[Project1].[ShipName] AS [ShipName],
[Project1].[ShipAddress] AS [ShipAddress],
[Project1].[ShipCity] AS [ShipCity],
[Project1].[ShipRegion] AS [ShipRegion],
[Project1].[ShipPostalCode] AS [ShipPostalCode],
[Project1].[ShipCountry] AS [ShipCountry]
FROM ( SELECT
      [Extent1].[CustomerID] AS [CustomerID],
       [Extent1].[CompanyName] AS [CompanyName],
       [Extent1].[ContactName] AS [ContactName],
       [Extent1].[ContactTitle] AS [ContactTitle],
       [Extent1].[Address] AS [Address],
       [Extent1].[City] AS [City],
       [Extent1].[Region] AS [Region],
       [Extent1].[PostalCode] AS [PostalCode],
       [Extent1].[Country] AS [Country],
       [Extent1].[Phone] AS [Phone],
       [Extent1].[Fax] AS [Fax],
      1 AS [C1],
       [Extent2].[OrderID] AS [OrderID],
       [Extent2].[CustomerID] AS [CustomerID1],
       [Extent2].[EmployeeID] AS [EmployeeID],
       [Extent2].[OrderDate] AS [OrderDate],
       [Extent2].[RequiredDate] AS [RequiredDate],
       [Extent2].[ShippedDate] AS [ShippedDate],
       [Extent2].[ShipVia] AS [ShipVia],
       [Extent2].[Freight] AS [Freight],
       [Extent2].[ShipName] AS [ShipName],
       [Extent2].[ShipAddress] AS [ShipAddress],
       [Extent2].[ShipCity] AS [ShipCity],
       [Extent2].[ShipRegion] AS [ShipRegion],
       [Extent2].[ShipPostalCode] AS [ShipPostalCode],
       [Extent2].[ShipCountry] AS [ShipCountry],
      CASE WHEN ([Extent2].[OrderID] IS NULL) THEN CAST(NULL AS int) ELSE 1 END AS [C2]
      FROM  [dbo].[Customers] AS [Extent1]
      LEFT OUTER JOIN [dbo].[Orders] AS [Extent2] ON [Extent1].[CustomerID] = [Extent2].[CustomerID]
      WHERE N'UK' = [Extent1].[Country]
)  AS [Project1]
ORDER BY [Project1].[CustomerID] ASC, [Project1].[C2] ASC
```

<span data-ttu-id="eeb91-918">使用消極式載入，當您將一開始發出下列查詢：</span><span class="sxs-lookup"><span data-stu-id="eeb91-918">When using lazy loading, you'll issue the following query initially:</span></span>

``` SQL
SELECT
[Extent1].[CustomerID] AS [CustomerID],
[Extent1].[CompanyName] AS [CompanyName],
[Extent1].[ContactName] AS [ContactName],
[Extent1].[ContactTitle] AS [ContactTitle],
[Extent1].[Address] AS [Address],
[Extent1].[City] AS [City],
[Extent1].[Region] AS [Region],
[Extent1].[PostalCode] AS [PostalCode],
[Extent1].[Country] AS [Country],
[Extent1].[Phone] AS [Phone],
[Extent1].[Fax] AS [Fax]
FROM [dbo].[Customers] AS [Extent1]
WHERE N'UK' = [Extent1].[Country]
```

<span data-ttu-id="eeb91-919">然後，每次存取客戶的訂單導覽屬性對存放區，就會發出另一個查詢，如下所示：</span><span class="sxs-lookup"><span data-stu-id="eeb91-919">And each time you access the Orders navigation property of a customer another query like the following is issued against the store:</span></span>

``` SQL
exec sp_executesql N'SELECT
[Extent1].[OrderID] AS [OrderID],
[Extent1].[CustomerID] AS [CustomerID],
[Extent1].[EmployeeID] AS [EmployeeID],
[Extent1].[OrderDate] AS [OrderDate],
[Extent1].[RequiredDate] AS [RequiredDate],
[Extent1].[ShippedDate] AS [ShippedDate],
[Extent1].[ShipVia] AS [ShipVia],
[Extent1].[Freight] AS [Freight],
[Extent1].[ShipName] AS [ShipName],
[Extent1].[ShipAddress] AS [ShipAddress],
[Extent1].[ShipCity] AS [ShipCity],
[Extent1].[ShipRegion] AS [ShipRegion],
[Extent1].[ShipPostalCode] AS [ShipPostalCode],
[Extent1].[ShipCountry] AS [ShipCountry]
FROM [dbo].[Orders] AS [Extent1]
WHERE [Extent1].[CustomerID] = @EntityKeyValue1',N'@EntityKeyValue1 nchar(5)',@EntityKeyValue1=N'AROUT'
```

<span data-ttu-id="eeb91-920">如需詳細資訊，請參閱 <<c0> [ 載入相關物件](https://msdn.microsoft.com/library/bb896272.aspx)。</span><span class="sxs-lookup"><span data-stu-id="eeb91-920">For more information, see the [Loading Related Objects](https://msdn.microsoft.com/library/bb896272.aspx).</span></span>

#### <a name="821-lazy-loading-versus-eager-loading-cheat-sheet"></a><span data-ttu-id="eeb91-921">8.2.1 消極式載入與積極式載入小祕技</span><span class="sxs-lookup"><span data-stu-id="eeb91-921">8.2.1 Lazy Loading versus Eager Loading cheat sheet</span></span>

<span data-ttu-id="eeb91-922">沒有所謂一體適用選擇積極式載入和消極式載入的比較。</span><span class="sxs-lookup"><span data-stu-id="eeb91-922">There’s no such thing as a one-size-fits-all to choosing eager loading versus lazy loading.</span></span> <span data-ttu-id="eeb91-923">第一次嘗試了解這兩種策略，因此您可以執行也是明智的決策; 之間的差異此外，請考慮您的程式碼是否符合任何下列案例：</span><span class="sxs-lookup"><span data-stu-id="eeb91-923">Try first to understand the differences between both strategies so you can do a well informed decision; also, consider if your code fits to any of the following scenarios:</span></span>

| <span data-ttu-id="eeb91-924">情節</span><span class="sxs-lookup"><span data-stu-id="eeb91-924">Scenario</span></span>                                                                    | <span data-ttu-id="eeb91-925">我們建議</span><span class="sxs-lookup"><span data-stu-id="eeb91-925">Our Suggestion</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
|:----------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="eeb91-926">您需要從擷取的實體存取許多的導覽屬性嗎？</span><span class="sxs-lookup"><span data-stu-id="eeb91-926">Do you need to access many navigation properties from the fetched entities?</span></span> | <span data-ttu-id="eeb91-927">**否**-可能會進行這兩個選項。</span><span class="sxs-lookup"><span data-stu-id="eeb91-927">**No** - Both options will probably do.</span></span> <span data-ttu-id="eeb91-928">不過，如果將您的查詢內容不是太大，您可能會遇到效能優勢，使用積極式載入，因為它會在較不需要進行網路往返來具體化您的物件。</span><span class="sxs-lookup"><span data-stu-id="eeb91-928">However, if the payload your query is bringing is not too big, you may experience performance benefits by using Eager loading as it’ll require less network round trips to materialize your objects.</span></span> <br/> <br/> <span data-ttu-id="eeb91-929">**是**-如果您需要從實體存取許多的導覽屬性，一樣，使用多個 include 陳述式在查詢中使用積極式載入。</span><span class="sxs-lookup"><span data-stu-id="eeb91-929">**Yes** -  If you need to access many navigation properties from the entities, you’d do that by using multiple include statements in your query with Eager loading.</span></span> <span data-ttu-id="eeb91-930">包含多個實體，越大裝載您的查詢會傳回。</span><span class="sxs-lookup"><span data-stu-id="eeb91-930">The more entities you include, the bigger the payload your query will return.</span></span> <span data-ttu-id="eeb91-931">一旦您在查詢中包含三個或多個實體，請考慮改為載入的延遲。</span><span class="sxs-lookup"><span data-stu-id="eeb91-931">Once you include three or more entities into your query, consider switching to Lazy loading.</span></span> |
| <span data-ttu-id="eeb91-932">您知道哪些資料需要在執行階段的完全嗎？</span><span class="sxs-lookup"><span data-stu-id="eeb91-932">Do you know exactly what data will be needed at run time?</span></span>                   | <span data-ttu-id="eeb91-933">**否**-消極式載入會更好。</span><span class="sxs-lookup"><span data-stu-id="eeb91-933">**No** - Lazy loading will be better for you.</span></span> <span data-ttu-id="eeb91-934">否則，您可能會得到查詢資料，您不需要。</span><span class="sxs-lookup"><span data-stu-id="eeb91-934">Otherwise, you may end up querying for data that you will not need.</span></span> <br/> <br/> <span data-ttu-id="eeb91-935">**是**-積極式載入可能是最好的辦法，有助於更快載入整個集合。</span><span class="sxs-lookup"><span data-stu-id="eeb91-935">**Yes** - Eager loading is probably your best bet; it will help loading entire sets faster.</span></span> <span data-ttu-id="eeb91-936">如果您的查詢可讓您需要擷取非常大量的資料，而這變得太慢，請嘗試改為載入的延遲。</span><span class="sxs-lookup"><span data-stu-id="eeb91-936">If your query requires fetching a very large amount of data, and this becomes too slow, then try Lazy loading instead.</span></span>                                                                                                                                                                                                                                                       |
| <span data-ttu-id="eeb91-937">遠離您的資料庫執行的程式碼嗎？</span><span class="sxs-lookup"><span data-stu-id="eeb91-937">Is your code executing far from your database?</span></span> <span data-ttu-id="eeb91-938">（增加的網路延遲）</span><span class="sxs-lookup"><span data-stu-id="eeb91-938">(increased network latency)</span></span>  | <span data-ttu-id="eeb91-939">**否**-當網路延遲不是問題，請使用消極式載入可能會簡化您的程式碼。</span><span class="sxs-lookup"><span data-stu-id="eeb91-939">**No** - When the network latency is not an issue, using Lazy loading may simplify your code.</span></span> <span data-ttu-id="eeb91-940">請記住，您的應用程式的拓撲，可能會變更，因此不需要資料庫相近，授與。</span><span class="sxs-lookup"><span data-stu-id="eeb91-940">Remember that the topology of your application may change, so don’t take database proximity for granted.</span></span> <br/> <br/> <span data-ttu-id="eeb91-941">**是**-網路問題時，只有您可以決定更加符合您的案例。</span><span class="sxs-lookup"><span data-stu-id="eeb91-941">**Yes** - When the network is a problem, only you can decide what fits better for your scenario.</span></span> <span data-ttu-id="eeb91-942">通常積極式載入會是較佳因為它需要較少的來回行程。</span><span class="sxs-lookup"><span data-stu-id="eeb91-942">Typically Eager loading will be better because it requires fewer round trips.</span></span>                                                                                                                                                                                                      |


#### <a name="822-------performance-concerns-with-multiple-includes"></a><span data-ttu-id="eeb91-943">8.2.2 使用多個包含效能考量</span><span class="sxs-lookup"><span data-stu-id="eeb91-943">8.2.2       Performance concerns with multiple Includes</span></span>

<span data-ttu-id="eeb91-944">我們聽到了涉及伺服器回應時間問題的效能問題，問題的來源時經常使用多個 Include 陳述式查詢。</span><span class="sxs-lookup"><span data-stu-id="eeb91-944">When we hear performance questions that involve server response time problems, the source of the issue is frequently queries with multiple Include statements.</span></span> <span data-ttu-id="eeb91-945">雖然在查詢中包括相關的實體是功能強大，務必了解在幕後的事情。</span><span class="sxs-lookup"><span data-stu-id="eeb91-945">While including related entities in a query is powerful, it's important to understand what's happening under the covers.</span></span>

<span data-ttu-id="eeb91-946">花費較長的時間，針對具有多個 Include 陳述式的查詢來瀏覽我們的內部的計劃編譯器產生的存放區命令。</span><span class="sxs-lookup"><span data-stu-id="eeb91-946">It takes a relatively long time for a query with multiple Include statements in it to go through our internal plan compiler to produce the store command.</span></span> <span data-ttu-id="eeb91-947">這些時間大部分是花在嘗試最佳化產生的查詢。</span><span class="sxs-lookup"><span data-stu-id="eeb91-947">The majority of this time is spent trying to optimize the resulting query.</span></span> <span data-ttu-id="eeb91-948">產生的存放區命令將包含 Outer Join 或 Union，每個 include，根據您的對應。</span><span class="sxs-lookup"><span data-stu-id="eeb91-948">The generated store command will contain an Outer Join or Union for each Include, depending on your mapping.</span></span> <span data-ttu-id="eeb91-949">這類查詢會顯示在已連線的大型圖表中從您的資料庫，在單一的承載，將 acerbate 任何頻寬的問題，尤其是當有很多 （例如，當包含的多個層級用來周遊承載中的備援中的關聯來從一對多方向）。</span><span class="sxs-lookup"><span data-stu-id="eeb91-949">Queries like this will bring in large connected graphs from your database in a single payload, which will acerbate any bandwidth issues, especially when there is a lot of redundancy in the payload (for example, when multiple levels of Include are used to traverse associations in the one-to-many direction).</span></span>

<span data-ttu-id="eeb91-950">您可以檢查其中查詢會傳回過大承載 ToTraceString 的使用，並在 SQL Server Management Studio，若要查看的承載大小執行存放區命令，以存取基礎的 TSQL 查詢的情況。</span><span class="sxs-lookup"><span data-stu-id="eeb91-950">You can check for cases where your queries are returning excessively large payloads by accessing the underlying TSQL for the query by using ToTraceString and executing the store command in SQL Server Management Studio to see the payload size.</span></span> <span data-ttu-id="eeb91-951">在此情況下，您可以嘗試減少為只有在查詢中的 Include 陳述式的數目會納入所需的資料。</span><span class="sxs-lookup"><span data-stu-id="eeb91-951">In such cases you can try to reduce the number of Include statements in your query to just bring in the data you need.</span></span> <span data-ttu-id="eeb91-952">或者，您可以將查詢分割成較小的一連串的子查詢，例如：</span><span class="sxs-lookup"><span data-stu-id="eeb91-952">Or you may be able to break your query into a smaller sequence of subqueries, for example:</span></span>

<span data-ttu-id="eeb91-953">**之前的重大查詢：**</span><span class="sxs-lookup"><span data-stu-id="eeb91-953">**Before breaking the query:**</span></span>

``` csharp
using (NorthwindEntities context = new NorthwindEntities())
{
    var customers = from c in context.Customers.Include(c => c.Orders)
                    where c.LastName.StartsWith(lastNameParameter)
                    select c;

    foreach (Customer customer in customers)
    {
        ...
    }
}
```

<span data-ttu-id="eeb91-954">**之後的重大查詢：**</span><span class="sxs-lookup"><span data-stu-id="eeb91-954">**After breaking the query:**</span></span>

``` csharp
using (NorthwindEntities context = new NorthwindEntities())
{
    var orders = from o in context.Orders
                 where o.Customer.LastName.StartsWith(lastNameParameter)
                 select o;

    orders.Load();

    var customers = from c in context.Customers
                    where c.LastName.StartsWith(lastNameParameter)
                    select c;

    foreach (Customer customer in customers)
    {
        ...
    }
}
```

<span data-ttu-id="eeb91-955">這會適用於只追蹤查詢，因為我們正在使用的內容必須自動執行識別解析和關聯的修復能力。</span><span class="sxs-lookup"><span data-stu-id="eeb91-955">This will work only on tracked queries, as we are making use of the ability the context has to perform identity resolution and association fixup automatically.</span></span>

<span data-ttu-id="eeb91-956">如同消極式載入，代價會是較小的裝載多個查詢。</span><span class="sxs-lookup"><span data-stu-id="eeb91-956">As with lazy loading, the tradeoff will be more queries for smaller payloads.</span></span> <span data-ttu-id="eeb91-957">您也可以使用 投射的個別屬性，明確地從每個實體中，選取所需的資料，但您將不會載入實體在此情況下，並不支援更新。</span><span class="sxs-lookup"><span data-stu-id="eeb91-957">You can also use projections of individual properties to explicitly select only the data you need from each entity, but you will not be loading entities in this case, and updates will not be supported.</span></span>

#### <a name="823-workaround-to-get-lazy-loading-of-properties"></a><span data-ttu-id="eeb91-958">8.2.3 因應措施若要取得屬性的消極式載入</span><span class="sxs-lookup"><span data-stu-id="eeb91-958">8.2.3 Workaround to get lazy loading of properties</span></span>

<span data-ttu-id="eeb91-959">Entity Framework 目前不支援純量或複雜屬性的消極式的載入。</span><span class="sxs-lookup"><span data-stu-id="eeb91-959">Entity Framework currently doesn’t support lazy loading of scalar or complex properties.</span></span> <span data-ttu-id="eeb91-960">不過，在您用來有資料表，其中包含大型的物件，例如 BLOB 的情況下，您可以使用資料表分割分成個別的實體中的較大的屬性。</span><span class="sxs-lookup"><span data-stu-id="eeb91-960">However, in cases where you have a table that includes a large object such as a BLOB, you can use table splitting to separate the large properties into a separate entity.</span></span> <span data-ttu-id="eeb91-961">例如，假設您有 Product 資料表包含 varbinary photo 資料行。</span><span class="sxs-lookup"><span data-stu-id="eeb91-961">For example, suppose you have a Product table that includes a varbinary photo column.</span></span> <span data-ttu-id="eeb91-962">如果您沒有經常需要存取這個屬性，在查詢中的，您可以使用資料表分割，以便納入實體，您通常需要的部分。</span><span class="sxs-lookup"><span data-stu-id="eeb91-962">If you don't frequently need to access this property in your queries, you can use table splitting to bring in only the parts of the entity that you normally need.</span></span> <span data-ttu-id="eeb91-963">當您明確需要時，將只會載入代表產品相片的實體。</span><span class="sxs-lookup"><span data-stu-id="eeb91-963">The entity representing the product photo will only be loaded when you explicitly need it.</span></span>

<span data-ttu-id="eeb91-964">說明如何啟用資料表分割的絕佳資源是 Gil Fink 「 資料表分割中實體架構 」 部落格文章： \< http://blogs.microsoft.co.il/blogs/gilf/archive/2009/10/13/table-splitting-in-entity-framework.aspx>。</span><span class="sxs-lookup"><span data-stu-id="eeb91-964">A good resource that shows how to enable table splitting is Gil Fink's "Table Splitting in Entity Framework" blog post: \<http://blogs.microsoft.co.il/blogs/gilf/archive/2009/10/13/table-splitting-in-entity-framework.aspx>.</span></span>

## <a name="9-other-considerations"></a><span data-ttu-id="eeb91-965">9 其他考量</span><span class="sxs-lookup"><span data-stu-id="eeb91-965">9 Other considerations</span></span>

### <a name="91------server-garbage-collection"></a><span data-ttu-id="eeb91-966">9.1 伺服器記憶體回收</span><span class="sxs-lookup"><span data-stu-id="eeb91-966">9.1      Server Garbage Collection</span></span>

<span data-ttu-id="eeb91-967">某些使用者可能會遇到限制他們預期的記憶體回收行程的設定不正確的平行處理原則的資源爭用。</span><span class="sxs-lookup"><span data-stu-id="eeb91-967">Some users might experience resource contention that limits the parallelism they are expecting when the Garbage Collector is not properly configured.</span></span> <span data-ttu-id="eeb91-968">每當 EF 會在多執行緒案例中，或在任何應用程式，類似於伺服器端系統，請務必啟用伺服器記憶體回收。</span><span class="sxs-lookup"><span data-stu-id="eeb91-968">Whenever EF is used in a multithreaded scenario, or in any application that resembles a server-side system, make sure to enable Server Garbage Collection.</span></span> <span data-ttu-id="eeb91-969">這是透過簡單的設定，在您的應用程式組態檔中：</span><span class="sxs-lookup"><span data-stu-id="eeb91-969">This is done via a simple setting in your application config file:</span></span>

``` xml
<?xmlversion="1.0" encoding="utf-8" ?>
<configuration>
        <runtime>
               <gcServer enabled="true" />
        </runtime>
</configuration>
```

<span data-ttu-id="eeb91-970">這應該減少執行緒爭用，並在 CPU 飽和的情況下最多 30%的增加您的輸送量。</span><span class="sxs-lookup"><span data-stu-id="eeb91-970">This should decrease your thread contention and increase your throughput by up to 30% in CPU saturated scenarios.</span></span> <span data-ttu-id="eeb91-971">一般而言，您一定要測試您的應用程式的運作方式使用傳統記憶體回收 （其進一步調整 UI 和用戶端端案例中） 以及伺服器記憶體回收。</span><span class="sxs-lookup"><span data-stu-id="eeb91-971">In general terms, you should always test how your application behaves using the classic Garbage Collection (which is better tuned for UI and client side scenarios) as well as the Server Garbage Collection.</span></span>

### <a name="92------autodetectchanges"></a><span data-ttu-id="eeb91-972">9.2 AutoDetectChanges</span><span class="sxs-lookup"><span data-stu-id="eeb91-972">9.2      AutoDetectChanges</span></span>

<span data-ttu-id="eeb91-973">如先前所述，Entity Framework 可能會顯示效能問題，當物件快取具有許多實體。</span><span class="sxs-lookup"><span data-stu-id="eeb91-973">As mentioned earlier, Entity Framework might show performance issues when the object cache has many entities.</span></span> <span data-ttu-id="eeb91-974">某些作業，例如 Add、 Remove、 尋找、 項目和 SaveChanges，觸發程序呼叫 DetectChanges，這可能會耗用大量的基礎物件快取已變成多大的 CPU。</span><span class="sxs-lookup"><span data-stu-id="eeb91-974">Certain operations, such as Add, Remove, Find, Entry and SaveChanges, trigger calls to DetectChanges which might consume a large amount of CPU based on how large the object cache has become.</span></span> <span data-ttu-id="eeb91-975">這是物件快取，物件狀態管理員嘗試將保持同步處理每個執行的作業內容，以便能夠在各式各樣的案例下正確確保產生的資料越好。</span><span class="sxs-lookup"><span data-stu-id="eeb91-975">The reason for this is that the object cache and the object state manager try to stay as synchronized as possible on each operation performed to a context so that the produced data is guaranteed to be correct under a wide array of scenarios.</span></span>

<span data-ttu-id="eeb91-976">它通常是最好的作法是將啟用您的應用程式的整個生命週期的 Entity Framework 自動變更偵測。</span><span class="sxs-lookup"><span data-stu-id="eeb91-976">It is generally a good practice to leave Entity Framework’s automatic change detection enabled for the entire life of your application.</span></span> <span data-ttu-id="eeb91-977">如果您的案例受高 CPU 使用量造成負面影響，而且您的設定檔可讓您表示問題是呼叫 DetectChanges，請考慮暫時關閉 AutoDetectChanges，在您的程式碼的敏感部分：</span><span class="sxs-lookup"><span data-stu-id="eeb91-977">If your scenario is being negatively affected by high CPU usage and your profiles indicate that the culprit is the call to DetectChanges, consider temporarily turning off AutoDetectChanges in the sensitive portion of your code:</span></span>

``` csharp
try
{
    context.Configuration.AutoDetectChangesEnabled = false;
    var product = context.Products.Find(productId);
    ...
}
finally
{
    context.Configuration.AutoDetectChangesEnabled = true;
}
```

<span data-ttu-id="eeb91-978">關閉前 AutoDetectChanges，最好了解這可能會導致失去其同時可追蹤特定資訊將會在實體的變更的 Entity Framework。</span><span class="sxs-lookup"><span data-stu-id="eeb91-978">Before turning off AutoDetectChanges, it’s good to understand that this might cause Entity Framework to lose its ability to track certain information about the changes that are taking place on the entities.</span></span> <span data-ttu-id="eeb91-979">如果不正確地處理，這可能造成資料不一致，在您的應用程式。</span><span class="sxs-lookup"><span data-stu-id="eeb91-979">If handled incorrectly, this might cause data inconsistency on your application.</span></span> <span data-ttu-id="eeb91-980">如需有關如何關閉 AutoDetectChanges 的詳細資訊，請閱讀\< http://blog.oneunicorn.com/2012/03/12/secrets-of-detectchanges-part-3-switching-off-automatic-detectchanges/>。</span><span class="sxs-lookup"><span data-stu-id="eeb91-980">For more information on turning off AutoDetectChanges, read \<http://blog.oneunicorn.com/2012/03/12/secrets-of-detectchanges-part-3-switching-off-automatic-detectchanges/>.</span></span>

### <a name="93------context-per-request"></a><span data-ttu-id="eeb91-981">9.3 每個要求的內容</span><span class="sxs-lookup"><span data-stu-id="eeb91-981">9.3      Context per request</span></span>

<span data-ttu-id="eeb91-982">Entity Framework 內容應該要當做存留較短的執行個體，以提供最佳效能體驗。</span><span class="sxs-lookup"><span data-stu-id="eeb91-982">Entity Framework’s contexts are meant to be used as short-lived instances in order to provide the most optimal performance experience.</span></span> <span data-ttu-id="eeb91-983">內容應該是短暫存留時間，並捨棄，並因此實作非常輕量和 reutilize 盡可能的中繼資料。</span><span class="sxs-lookup"><span data-stu-id="eeb91-983">Contexts are expected to be short lived and discarded, and as such have been implemented to be very lightweight and reutilize metadata whenever possible.</span></span> <span data-ttu-id="eeb91-984">在 web 案例中很重要要謹記在心，在單一要求的持續時間超過沒有內容。</span><span class="sxs-lookup"><span data-stu-id="eeb91-984">In web scenarios it’s important to keep this in mind and not have a context for more than the duration of a single request.</span></span> <span data-ttu-id="eeb91-985">同樣地，在非 web 案例中，內容應捨棄根據不同的層級的快取在 Entity Framework 了解。</span><span class="sxs-lookup"><span data-stu-id="eeb91-985">Similarly, in non-web scenarios, context should be discarded based on your understanding of the different levels of caching in the Entity Framework.</span></span> <span data-ttu-id="eeb91-986">一般而言，其中一個應該避免在整個生命週期的應用程式，以及每個執行緒的內容和靜態內容的內容執行個體。</span><span class="sxs-lookup"><span data-stu-id="eeb91-986">Generally speaking, one should avoid having a context instance throughout the life of the application, as well as contexts per thread and static contexts.</span></span>

### <a name="94------database-null-semantics"></a><span data-ttu-id="eeb91-987">9.4 資料庫 null 語意</span><span class="sxs-lookup"><span data-stu-id="eeb91-987">9.4      Database null semantics</span></span>

<span data-ttu-id="eeb91-988">預設的 entity Framework 會產生具有 C 的 SQL 程式碼\#null 比較語意。</span><span class="sxs-lookup"><span data-stu-id="eeb91-988">Entity Framework by default will generate SQL code that has C\# null comparison semantics.</span></span> <span data-ttu-id="eeb91-989">請考慮下列範例查詢：</span><span class="sxs-lookup"><span data-stu-id="eeb91-989">Consider the following example query:</span></span>

``` csharp
            int? categoryId = 7;
            int? supplierId = 8;
            decimal? unitPrice = 0;
            short? unitsInStock = 100;
            short? unitsOnOrder = 20;
            short? reorderLevel = null;

            var q = from p incontext.Products
                    wherep.Category.CategoryName == "Beverages"
                          || (p.CategoryID == categoryId
                                || p.SupplierID == supplierId
                                || p.UnitPrice == unitPrice
                                || p.UnitsInStock == unitsInStock
                                || p.UnitsOnOrder == unitsOnOrder
                                || p.ReorderLevel == reorderLevel)
                    select p;

            var r = q.ToList();
```

<span data-ttu-id="eeb91-990">在此範例中，我們要比較數個可為 null 的變數，針對實體，例如 SupplierID 和 UnitPrice 可為 null 的屬性。</span><span class="sxs-lookup"><span data-stu-id="eeb91-990">In this example, we’re comparing a number of nullable variables against nullable properties on the entity, such as SupplierID and UnitPrice.</span></span> <span data-ttu-id="eeb91-991">如果參數值的資料行值相同，則參數和資料行值都是 null，則會要求這項查詢產生的 SQL。</span><span class="sxs-lookup"><span data-stu-id="eeb91-991">The generated SQL for this query will ask if the parameter value is the same as the column value, or if both the parameter and the column values are null.</span></span> <span data-ttu-id="eeb91-992">這會隱藏資料庫伺服器處理 null 值的方式，也會提供一致的 C\# null 跨不同的資料庫供應商的體驗。</span><span class="sxs-lookup"><span data-stu-id="eeb91-992">This will hide the way the database server handles nulls and will provide a consistent C\# null experience across different database vendors.</span></span> <span data-ttu-id="eeb91-993">相反地，產生的程式碼一點迂迴，無法執行時，也比較量大的數字，在 where 成長查詢陳述式。</span><span class="sxs-lookup"><span data-stu-id="eeb91-993">On the other hand, the generated code is a bit convoluted and may not perform well when the amount of comparisons in the where statement of the query grows to a large number.</span></span>

<span data-ttu-id="eeb91-994">處理這種情況的一個方式是使用資料庫 null 語意。</span><span class="sxs-lookup"><span data-stu-id="eeb91-994">One way to deal with this situation is by using database null semantics.</span></span> <span data-ttu-id="eeb91-995">請注意，這可能會潛在的行為不同於 C\# null 語意之後, 現在 Entity Framework 會產生更簡單的 SQL 資料庫引擎處理 null 值的方式公開 （expose)。</span><span class="sxs-lookup"><span data-stu-id="eeb91-995">Note that this might potentially behave differently to the C\# null semantics since now Entity Framework will generate simpler SQL that exposes the way the database engine handles null values.</span></span> <span data-ttu-id="eeb91-996">資料庫 null 語意，可以是啟動每個內容與內容組態對單一組態一行：</span><span class="sxs-lookup"><span data-stu-id="eeb91-996">Database null semantics can be activated per-context with one single configuration line against the context configuration:</span></span>

``` csharp
                context.Configuration.UseDatabaseNullSemantics = true;
```

<span data-ttu-id="eeb91-997">小型到中型大小的查詢將不會顯示可察覺的效能改進時使用資料庫 null 語意，但是差異將會更明顯且含有大量潛在 null 比較的查詢。</span><span class="sxs-lookup"><span data-stu-id="eeb91-997">Small to medium sized queries will not display a perceptible performance improvement when using database null semantics, but the difference will become more noticeable on queries with a large number of potential null comparisons.</span></span>

<span data-ttu-id="eeb91-998">在上述範例查詢中，效能差異雖然很低於 2 %microbenchmark，在受控制的環境中執行中。</span><span class="sxs-lookup"><span data-stu-id="eeb91-998">In the example query above, the performance difference was less than 2% in a microbenchmark running in a controlled environment.</span></span>

### <a name="95------async"></a><span data-ttu-id="eeb91-999">9.5 非同步</span><span class="sxs-lookup"><span data-stu-id="eeb91-999">9.5      Async</span></span>

<span data-ttu-id="eeb91-1000">Entity Framework 6 引進了支援的執行在.NET 4.5 或更新版本時的非同步作業。</span><span class="sxs-lookup"><span data-stu-id="eeb91-1000">Entity Framework 6 introduced support of async operations when running on .NET 4.5 or later.</span></span> <span data-ttu-id="eeb91-1001">大部分的情況下，使 IO 的應用程式相關的競爭會受益於最多使用非同步查詢並儲存作業。</span><span class="sxs-lookup"><span data-stu-id="eeb91-1001">For the most part, applications that have IO related contention will benefit the most from using asynchronous query and save operations.</span></span> <span data-ttu-id="eeb91-1002">如果您的應用程式不會發生從 IO 爭用，非同步處理使用，在最佳的情況下，以同步方式執行和傳回結果，在相同的時間與同步呼叫，或最糟的情況、 只是延後執行非同步工作，並新增額外的 time 完成您的案例。</span><span class="sxs-lookup"><span data-stu-id="eeb91-1002">If your application does not suffer from IO contention, the use of async will, in the best cases, run synchronously and return the result in the same amount of time as a synchronous call, or in the worst case, simply defer execution to an asynchronous task and add extra time to the completion of your scenario.</span></span>

<span data-ttu-id="eeb91-1003">如需如何非同步的程式設計工作可協助您決定是否非同步會改善您的應用程式的效能請瀏覽[ http://msdn.microsoft.com/library/hh191443.aspx ](https://msdn.microsoft.com/library/hh191443.aspx)。</span><span class="sxs-lookup"><span data-stu-id="eeb91-1003">For information on how asynchronous programming work that will help you deciding if async will improve the performance of your application visit [http://msdn.microsoft.com/library/hh191443.aspx](https://msdn.microsoft.com/library/hh191443.aspx).</span></span> <span data-ttu-id="eeb91-1004">如需使用 Entity Framework 上的非同步作業的詳細資訊，請參閱 <<c0> [ 非同步查詢和儲存](~/ef6/fundamentals/async.md
)。</span><span class="sxs-lookup"><span data-stu-id="eeb91-1004">For more information on the use of async operations on Entity Framework, see [Async Query and Save](~/ef6/fundamentals/async.md
).</span></span>

### <a name="96------ngen"></a><span data-ttu-id="eeb91-1005">9.6 NGEN</span><span class="sxs-lookup"><span data-stu-id="eeb91-1005">9.6      NGEN</span></span>

<span data-ttu-id="eeb91-1006">Entity Framework 6 不會在.NET framework 的預設安裝中。</span><span class="sxs-lookup"><span data-stu-id="eeb91-1006">Entity Framework 6 does not come in the default installation of .NET framework.</span></span> <span data-ttu-id="eeb91-1007">因此，Entity Framework 組件不會根據預設，這表示所有 Entity Framework 程式碼，受都限於相同 JIT'ing 成本為其他 MSIL 組件的 NGEN。</span><span class="sxs-lookup"><span data-stu-id="eeb91-1007">As such, the Entity Framework assemblies are not NGEN’d by default which means that all the Entity Framework code is subject to the same JIT’ing costs as any other MSIL assembly.</span></span> <span data-ttu-id="eeb91-1008">這可能會降低的 F5 體驗，同時開發以及您在生產環境中的應用程式的冷啟動。</span><span class="sxs-lookup"><span data-stu-id="eeb91-1008">This might degrade the F5 experience while developing and also the cold startup of your application in the production environments.</span></span> <span data-ttu-id="eeb91-1009">若要降低 CPU 和記憶體的成本 JIT'ing 就建議您在 Entity Framework 映像為適當的 NGEN。</span><span class="sxs-lookup"><span data-stu-id="eeb91-1009">In order to reduce the CPU and memory costs of JIT’ing it is advisable to NGEN the Entity Framework images as appropriate.</span></span> <span data-ttu-id="eeb91-1010">如需有關如何改善使用 NGEN 的 Entity Framework 6 的啟動效能的詳細資訊，請參閱 < [ngen 改善啟動效能](~/ef6/fundamentals/performance/ngen.md)。</span><span class="sxs-lookup"><span data-stu-id="eeb91-1010">For more information on how to improve the startup performance of Entity Framework 6 with NGEN, see [Improving Startup Performance with NGen](~/ef6/fundamentals/performance/ngen.md).</span></span>

### <a name="97------code-first-versus-edmx"></a><span data-ttu-id="eeb91-1011">9.7 版，code First 與 EDMX</span><span class="sxs-lookup"><span data-stu-id="eeb91-1011">9.7      Code First versus EDMX</span></span>

<span data-ttu-id="eeb91-1012">問題有關的阻抗不相符的物件導向程式設計與由概念模型 （物件）、 儲存結構描述 (database) 和之間的對應的記憶體中表示的關聯式資料庫之間的 entity Framework 原因兩個。</span><span class="sxs-lookup"><span data-stu-id="eeb91-1012">Entity Framework reasons about the impedance mismatch problem between object oriented programming and relational databases by having an in-memory representation of the conceptual model (the objects), the storage schema (the database) and a mapping between the two.</span></span> <span data-ttu-id="eeb91-1013">此中繼資料會呼叫簡短的實體資料模型或 EDM。</span><span class="sxs-lookup"><span data-stu-id="eeb91-1013">This metadata is called an Entity Data Model, or EDM for short.</span></span> <span data-ttu-id="eeb91-1014">此 EDM 中，從 Entity Framework 會衍生往返的資料的檢視，從資料庫的記憶體中的物件，並備份。</span><span class="sxs-lookup"><span data-stu-id="eeb91-1014">From this EDM, Entity Framework will derive the views to roundtrip data from the objects in memory to the database and back.</span></span>

<span data-ttu-id="eeb91-1015">當 Entity Framework 搭配使用 EDMX 檔案也正式指定概念模型、 儲存結構描述和對應，則模型載入階段只有來驗證 EDM 是否正確 （例如，請確定沒有對應沒有），然後產生檢視，然後驗證的檢視，並已備妥此中繼資料。</span><span class="sxs-lookup"><span data-stu-id="eeb91-1015">When Entity Framework is used with an EDMX file that formally specifies the conceptual model, the storage schema, and the mapping, then the model loading stage only has to validate that the EDM is correct (for example, make sure that no mappings are missing), then generate the views, then validate the views and have this metadata ready for use.</span></span> <span data-ttu-id="eeb91-1016">只執行然後可以查詢或新的資料儲存至資料存放區。</span><span class="sxs-lookup"><span data-stu-id="eeb91-1016">Only then can a query be executed or new data be saved to the data store.</span></span>

<span data-ttu-id="eeb91-1017">Code First 方法是，它的核心，複雜的實體資料模型產生器。</span><span class="sxs-lookup"><span data-stu-id="eeb91-1017">The Code First approach is, at its heart, a sophisticated Entity Data Model generator.</span></span> <span data-ttu-id="eeb91-1018">Entity Framework 必須產生 EDM，從提供的程式碼;它是藉由分析在模型中，套用慣例和設定此模型透過 Fluent API 所涉及的類別。</span><span class="sxs-lookup"><span data-stu-id="eeb91-1018">The Entity Framework has to produce an EDM from the provided code; it does so by analyzing the classes involved in the model, applying conventions and configuring the model via the Fluent API.</span></span> <span data-ttu-id="eeb91-1019">建立 EDM 之後，Entity Framework 基本上行為相同方式會有 EDMX 檔案已存在於專案中。</span><span class="sxs-lookup"><span data-stu-id="eeb91-1019">After the EDM is built, the Entity Framework essentially behaves the same way as it would had an EDMX file been present in the project.</span></span> <span data-ttu-id="eeb91-1020">因此，從 Code First 建立模型所增加的 Entity framework，相較於具有 EDMX 會轉譯成速度較慢的啟動時間的額外複雜性。</span><span class="sxs-lookup"><span data-stu-id="eeb91-1020">Thus, building the model from Code First adds extra complexity that translates into a slower startup time for the Entity Framework when compared to having an EDMX.</span></span> <span data-ttu-id="eeb91-1021">成本是完全取決於大小和正在建置之模型的複雜度。</span><span class="sxs-lookup"><span data-stu-id="eeb91-1021">The cost is completely dependent on the size and complexity of the model that’s being built.</span></span>

<span data-ttu-id="eeb91-1022">在選擇要使用 Code First 與 EDMX 時，務必知道，Code First 所導入的彈性，將會增加第一次建立模型的成本。</span><span class="sxs-lookup"><span data-stu-id="eeb91-1022">When choosing to use EDMX versus Code First, it’s important to know that the flexibility introduced by Code First increases the cost of building the model for the first time.</span></span> <span data-ttu-id="eeb91-1023">如果您的應用程式可以承受此第一次負載的成本則通常 Code First 會前往的慣用的方法。</span><span class="sxs-lookup"><span data-stu-id="eeb91-1023">If your application can withstand the cost of this first-time load then typically Code First will be the preferred way to go.</span></span>

## <a name="10-investigating-performance"></a><span data-ttu-id="eeb91-1024">10 調查效能</span><span class="sxs-lookup"><span data-stu-id="eeb91-1024">10 Investigating Performance</span></span>

### <a name="101-using-the-visual-studio-profiler"></a><span data-ttu-id="eeb91-1025">10.1 使用 Visual Studio Profiler</span><span class="sxs-lookup"><span data-stu-id="eeb91-1025">10.1 Using the Visual Studio Profiler</span></span>

<span data-ttu-id="eeb91-1026">如果您有使用 Entity Framework 的效能問題，您可以使用像建置到 Visual Studio 分析工具，以查看應用程式花費的時間。</span><span class="sxs-lookup"><span data-stu-id="eeb91-1026">If you are having performance issues with the Entity Framework, you can use a profiler like the one built into Visual Studio to see where your application is spending its time.</span></span> <span data-ttu-id="eeb91-1027">這是我們用來產生圓形圖 」 瀏覽 ADO.NET Entity Framework 的效能-第 1 部分 」 部落格文章中的工具 ( \< http://blogs.msdn.com/b/adonet/archive/2008/02/04/exploring-the-performance-of-the-ado-net-entity-framework-part-1.aspx>)說明 Entity Framework 要花費在它在冷和暖查詢期間的時間。</span><span class="sxs-lookup"><span data-stu-id="eeb91-1027">This is the tool we used to generate the pie charts in the “Exploring the Performance of the ADO.NET Entity Framework - Part 1” blog post ( \<http://blogs.msdn.com/b/adonet/archive/2008/02/04/exploring-the-performance-of-the-ado-net-entity-framework-part-1.aspx>) that show where Entity Framework spends its time during cold and warm queries.</span></span>

<span data-ttu-id="eeb91-1028">資料和模型化客戶諮詢小組所撰寫的 「 使用 Visual Studio 2010 Profiler 分析 Entity Framework 」 部落格文章顯示如何它們使用分析工具來調查效能問題的真實世界範例。</span><span class="sxs-lookup"><span data-stu-id="eeb91-1028">The "Profiling Entity Framework using the Visual Studio 2010 Profiler" blog post written by the Data and Modeling Customer Advisory Team shows a real-world example of how they used the profiler to investigate a performance problem.</span></span>  <span data-ttu-id="eeb91-1029">\<http://blogs.msdn.com/b/dmcat/archive/2010/04/30/profiling-entity-framework-using-the-visual-studio-2010-profiler.aspx>。</span><span class="sxs-lookup"><span data-stu-id="eeb91-1029">\<http://blogs.msdn.com/b/dmcat/archive/2010/04/30/profiling-entity-framework-using-the-visual-studio-2010-profiler.aspx>.</span></span> <span data-ttu-id="eeb91-1030">這篇文章是針對 windows 應用程式所撰寫。</span><span class="sxs-lookup"><span data-stu-id="eeb91-1030">This post was written for a windows application.</span></span> <span data-ttu-id="eeb91-1031">如果您要分析 web 應用程式的 Windows 效能記錄程式 (WPR) 和 Windows Performance Analyzer (WPA) 工具可能會優於從 Visual Studio 工作運作。</span><span class="sxs-lookup"><span data-stu-id="eeb91-1031">If you need to profile a web application the Windows Performance Recorder (WPR) and Windows Performance Analyzer (WPA) tools may work better than working from Visual Studio.</span></span> <span data-ttu-id="eeb91-1032">WPR 和 WPA 屬於 Windows 效能工具組所附含的 Windows 評定及部署套件 ( [ http://www.microsoft.com/en-US/download/details.aspx?id=39982 ](https://www.microsoft.com/en-US/download/details.aspx?id=39982))。</span><span class="sxs-lookup"><span data-stu-id="eeb91-1032">WPR and WPA are part of the Windows Performance Toolkit which is included with the Windows Assessment and Deployment Kit ( [http://www.microsoft.com/en-US/download/details.aspx?id=39982](https://www.microsoft.com/en-US/download/details.aspx?id=39982)).</span></span>

### <a name="102-applicationdatabase-profiling"></a><span data-ttu-id="eeb91-1033">10.2 應用程式/資料庫程式碼剖析</span><span class="sxs-lookup"><span data-stu-id="eeb91-1033">10.2 Application/Database profiling</span></span>

<span data-ttu-id="eeb91-1034">內建於 Visual Studio 分析工具等工具會告訴您應用程式花費的時間。</span><span class="sxs-lookup"><span data-stu-id="eeb91-1034">Tools like the profiler built into Visual Studio tell you where your application is spending time.</span></span>  <span data-ttu-id="eeb91-1035">程式碼剖析工具的另一種是可用的執行動態分析執行中應用程式，在實際執行或根據需求，進入生產階段前的，並尋找常見的錯誤和反向模式的資料庫存取權。</span><span class="sxs-lookup"><span data-stu-id="eeb91-1035">Another type of profiler is available that performs dynamic analysis of your running application, either in production or pre-production depending on needs, and looks for common pitfalls and anti-patterns of database access.</span></span>

<span data-ttu-id="eeb91-1036">兩個販售的分析工具是 Entity Framework Profiler ( \< http://efprof.com>) ORMProfiler 和 ( \< http://ormprofiler.com>)。</span><span class="sxs-lookup"><span data-stu-id="eeb91-1036">Two commercially available profilers are the Entity Framework Profiler ( \<http://efprof.com>) and ORMProfiler ( \<http://ormprofiler.com>).</span></span>

<span data-ttu-id="eeb91-1037">如果您的應用程式是使用 Code First 在 MVC 應用程式，您可以使用 StackExchange 的 MiniProfiler。</span><span class="sxs-lookup"><span data-stu-id="eeb91-1037">If your application is an MVC application using Code First, you can use StackExchange's MiniProfiler.</span></span> <span data-ttu-id="eeb91-1038">Scott Hanselman 說明這項工具在他的部落格： \< http://www.hanselman.com/blog/NuGetPackageOfTheWeek9ASPNETMiniProfilerFromStackExchangeRocksYourWorld.aspx>。</span><span class="sxs-lookup"><span data-stu-id="eeb91-1038">Scott Hanselman describes this tool in his blog at: \<http://www.hanselman.com/blog/NuGetPackageOfTheWeek9ASPNETMiniProfilerFromStackExchangeRocksYourWorld.aspx>.</span></span>

<span data-ttu-id="eeb91-1039">如需有關您的應用程式資料庫活動，Julie Lerman 的 MSDN Magazine 文章標題，請參閱程式碼剖析[Entity Framework 中的分析資料庫活動](https://msdn.microsoft.com/magazine/gg490349.aspx)。</span><span class="sxs-lookup"><span data-stu-id="eeb91-1039">For more information on profiling your application's database activity, see Julie Lerman's MSDN Magazine article titled [Profiling Database Activity in the Entity Framework](https://msdn.microsoft.com/magazine/gg490349.aspx).</span></span>

### <a name="103-database-logger"></a><span data-ttu-id="eeb91-1040">10.3 資料庫記錄器</span><span class="sxs-lookup"><span data-stu-id="eeb91-1040">10.3 Database logger</span></span>

<span data-ttu-id="eeb91-1041">如果您使用 Entity Framework 6 也請考慮使用內建記錄功能。</span><span class="sxs-lookup"><span data-stu-id="eeb91-1041">If you are using Entity Framework 6 also consider using the built-in logging functionality.</span></span> <span data-ttu-id="eeb91-1042">透過簡單的一行設定其活動記錄，即可指示內容的 資料庫屬性：</span><span class="sxs-lookup"><span data-stu-id="eeb91-1042">The Database property of the context can be instructed to log its activity via a simple one-line configuration:</span></span>

``` csharp
    using (var context = newQueryComparison.DbC.NorthwindEntities())
    {
        context.Database.Log = Console.WriteLine;
        var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
        q.ToList();
    }
```

<span data-ttu-id="eeb91-1043">在此範例中的資料庫活動將會記錄至主控台，但您可以設定記錄檔的內容呼叫任何動作&lt;字串&gt;委派。</span><span class="sxs-lookup"><span data-stu-id="eeb91-1043">In this example the database activity will be logged to the console, but the Log property can be configured to call any Action&lt;string&gt; delegate.</span></span>

<span data-ttu-id="eeb91-1044">如果您想要啟用資料庫記錄，而不需要重新編譯，而且您使用 Entity Framework 6.1 或更新版本中，則可以藉由新增您的應用程式的 web.config 或 app.config 檔案中的攔截器。</span><span class="sxs-lookup"><span data-stu-id="eeb91-1044">If you want to enable database logging without recompiling, and you are using Entity Framework 6.1 or later, you can do so by adding an interceptor in the web.config or app.config file of your application.</span></span>

``` xml
  <interceptors>
    <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework">
      <parameters>
        <parameter value="C:\Path\To\My\LogOutput.txt"/>
      </parameters>
    </interceptor>
  </interceptors>
```

<span data-ttu-id="eeb91-1045">如需有關如何不需要重新編譯移至 新增記錄\< http://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/>。</span><span class="sxs-lookup"><span data-stu-id="eeb91-1045">For more information on how to add logging without recompiling go to \<http://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/>.</span></span>

## <a name="11-appendix"></a><span data-ttu-id="eeb91-1046">11 的附錄</span><span class="sxs-lookup"><span data-stu-id="eeb91-1046">11 Appendix</span></span>

### <a name="111-a-test-environment"></a><span data-ttu-id="eeb91-1047">11.1 A.測試環境</span><span class="sxs-lookup"><span data-stu-id="eeb91-1047">11.1 A. Test Environment</span></span>

<span data-ttu-id="eeb91-1048">這種環境會使用從用戶端應用程式在不同電腦上的資料庫中的 2 機器安裝程式。</span><span class="sxs-lookup"><span data-stu-id="eeb91-1048">This environment uses a 2-machine setup with the database on a separate machine from the client application.</span></span> <span data-ttu-id="eeb91-1049">機器會位於相同的機架，因此網路延遲是相對較低，但比單一電腦環境更逼真。</span><span class="sxs-lookup"><span data-stu-id="eeb91-1049">Machines are in the same rack, so network latency is relatively low, but more realistic than a single-machine environment.</span></span>

#### <a name="1111-------app-server"></a><span data-ttu-id="eeb91-1050">11.1.1 應用程式伺服器</span><span class="sxs-lookup"><span data-stu-id="eeb91-1050">11.1.1       App Server</span></span>

##### <a name="11111------software-environment"></a><span data-ttu-id="eeb91-1051">11.1.1.1 軟體環境</span><span class="sxs-lookup"><span data-stu-id="eeb91-1051">11.1.1.1      Software Environment</span></span>

-   <span data-ttu-id="eeb91-1052">Entity Framework 4 的軟體環境</span><span class="sxs-lookup"><span data-stu-id="eeb91-1052">Entity Framework 4 Software Environment</span></span>
    -   <span data-ttu-id="eeb91-1053">作業系統名稱： Windows Server 2008 R2 Enterprise SP1。</span><span class="sxs-lookup"><span data-stu-id="eeb91-1053">OS Name: Windows Server 2008 R2 Enterprise SP1.</span></span>
    -   <span data-ttu-id="eeb91-1054">Visual 的 Studio 2010 – Ultimate。</span><span class="sxs-lookup"><span data-stu-id="eeb91-1054">Visual Studio 2010 – Ultimate.</span></span>
    -   <span data-ttu-id="eeb91-1055">Visual Studio 2010 SP1 （僅適用於某些比較）。</span><span class="sxs-lookup"><span data-stu-id="eeb91-1055">Visual Studio 2010 SP1 (only for some comparisons).</span></span>
-   <span data-ttu-id="eeb91-1056">Entity Framework 5 和 6 軟體環境</span><span class="sxs-lookup"><span data-stu-id="eeb91-1056">Entity Framework 5 and 6 Software Environment</span></span>
    -   <span data-ttu-id="eeb91-1057">作業系統名稱： Windows 8.1 企業版</span><span class="sxs-lookup"><span data-stu-id="eeb91-1057">OS Name: Windows 8.1 Enterprise</span></span>
    -   <span data-ttu-id="eeb91-1058">Visual Studio 2013 – Ultimate。</span><span class="sxs-lookup"><span data-stu-id="eeb91-1058">Visual Studio 2013 – Ultimate.</span></span>

##### <a name="11112------hardware-environment"></a><span data-ttu-id="eeb91-1059">11.1.1.2 硬體環境</span><span class="sxs-lookup"><span data-stu-id="eeb91-1059">11.1.1.2      Hardware Environment</span></span>

-   <span data-ttu-id="eeb91-1060">雙處理器: @ 2.27 GHz 的 intel （) mhz,8 CPU L5520 W3530、 2261 Mhz8 GHz、 4 個核心、 84 邏輯處理器。</span><span class="sxs-lookup"><span data-stu-id="eeb91-1060">Dual Processor:     Intel(R) Xeon(R) CPU L5520 W3530 @ 2.27GHz, 2261 Mhz8 GHz, 4 Core(s), 84 Logical Processor(s).</span></span>
-   <span data-ttu-id="eeb91-1061">2412 GB RamRAM。</span><span class="sxs-lookup"><span data-stu-id="eeb91-1061">2412 GB RamRAM.</span></span>
-   <span data-ttu-id="eeb91-1062">136 GB SCSI250GB SATA 7200 rpm 3 GB/s 磁碟機將分割成 4 個磁碟分割。</span><span class="sxs-lookup"><span data-stu-id="eeb91-1062">136 GB SCSI250GB SATA 7200 rpm 3GB/s drive split into 4 partitions.</span></span>

#### <a name="1112-------db-server"></a><span data-ttu-id="eeb91-1063">11.1.2 DB 伺服器</span><span class="sxs-lookup"><span data-stu-id="eeb91-1063">11.1.2       DB server</span></span>

##### <a name="11121------software-environment"></a><span data-ttu-id="eeb91-1064">11.1.2.1 軟體環境</span><span class="sxs-lookup"><span data-stu-id="eeb91-1064">11.1.2.1      Software Environment</span></span>

-   <span data-ttu-id="eeb91-1065">作業系統名稱： Windows Server 2008 R28.1 Enterprise SP1。</span><span class="sxs-lookup"><span data-stu-id="eeb91-1065">OS Name: Windows Server 2008 R28.1 Enterprise SP1.</span></span>
-   <span data-ttu-id="eeb91-1066">SQL Server 2008 R22012。</span><span class="sxs-lookup"><span data-stu-id="eeb91-1066">SQL Server 2008 R22012.</span></span>

##### <a name="11122------hardware-environment"></a><span data-ttu-id="eeb91-1067">11.1.2.2 硬體環境</span><span class="sxs-lookup"><span data-stu-id="eeb91-1067">11.1.2.2      Hardware Environment</span></span>

-   <span data-ttu-id="eeb91-1068">單一處理器： Intel （) mhz,8 CPU 2.27 GHz L5520，2261 MhzES-1620 0 @ 3.60 GHz，4 個核心，8 的邏輯處理器。</span><span class="sxs-lookup"><span data-stu-id="eeb91-1068">Single Processor: Intel(R) Xeon(R) CPU L5520  @ 2.27GHz, 2261 MhzES-1620 0 @ 3.60GHz, 4 Core(s), 8 Logical Processor(s).</span></span>
-   <span data-ttu-id="eeb91-1069">824 GB RamRAM。</span><span class="sxs-lookup"><span data-stu-id="eeb91-1069">824 GB RamRAM.</span></span>
-   <span data-ttu-id="eeb91-1070">465 GB ATA500GB SATA 7200 rpm 6 GB/s 磁碟機將分割成 4 個磁碟分割。</span><span class="sxs-lookup"><span data-stu-id="eeb91-1070">465 GB ATA500GB SATA 7200 rpm 6GB/s drive split into 4 partitions.</span></span>

### <a name="112------b-query-performance-comparison-tests"></a><span data-ttu-id="eeb91-1071">11.2 B.查詢效能比較測試</span><span class="sxs-lookup"><span data-stu-id="eeb91-1071">11.2      B. Query performance comparison tests</span></span>

<span data-ttu-id="eeb91-1072">Northwind 模型用來執行這些測試。</span><span class="sxs-lookup"><span data-stu-id="eeb91-1072">The Northwind model was used to execute these tests.</span></span> <span data-ttu-id="eeb91-1073">使用 Entity Framework 設計工具，從資料庫產生。</span><span class="sxs-lookup"><span data-stu-id="eeb91-1073">It was generated from the database using the Entity Framework designer.</span></span> <span data-ttu-id="eeb91-1074">然後，下列程式碼可用來比較查詢執行選項的效能：</span><span class="sxs-lookup"><span data-stu-id="eeb91-1074">Then, the following code was used to compare the performance of the query execution options:</span></span>

``` csharp
using System;
using System.Collections.Generic;
using System.Data;
using System.Data.Common;
using System.Data.Entity.Infrastructure;
using System.Data.EntityClient;
using System.Data.Objects;
using System.Linq;

namespace QueryComparison
{
    public partial class NorthwindEntities : ObjectContext
    {
        private static readonly Func<NorthwindEntities, string, IQueryable<Product>> productsForCategoryCQ = CompiledQuery.Compile(
            (NorthwindEntities context, string categoryName) =>
                context.Products.Where(p => p.Category.CategoryName == categoryName)
                );

        public IQueryable<Product> InvokeProductsForCategoryCQ(string categoryName)
        {
            return productsForCategoryCQ(this, categoryName);
        }
    }

    public class QueryTypePerfComparison
    {
        private static string entityConnectionStr = @"metadata=res://*/Northwind.csdl|res://*/Northwind.ssdl|res://*/Northwind.msl;provider=System.Data.SqlClient;provider connection string='data source=.;initial catalog=Northwind;integrated security=True;multipleactiveresultsets=True;App=EntityFramework'";

        public void LINQIncludingContextCreation()
        {
            using (NorthwindEntities context = new NorthwindEntities())
            {                 
                var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
                q.ToList();
            }
        }

        public void LINQNoTracking()
        {
            using (NorthwindEntities context = new NorthwindEntities())
            {
                context.Products.MergeOption = MergeOption.NoTracking;

                var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
                q.ToList();
            }
        }

        public void CompiledQuery()
        {
            using (NorthwindEntities context = new NorthwindEntities())
            {
                var q = context.InvokeProductsForCategoryCQ("Beverages");
                q.ToList();
            }
        }

        public void ObjectQuery()
        {
            using (NorthwindEntities context = new NorthwindEntities())
            {
                ObjectQuery<Product> products = context.Products.Where("it.Category.CategoryName = 'Beverages'");
                products.ToList();
            }
        }

        public void EntityCommand()
        {
            using (EntityConnection eConn = new EntityConnection(entityConnectionStr))
            {
                eConn.Open();
                EntityCommand cmd = eConn.CreateCommand();
                cmd.CommandText = "Select p From NorthwindEntities.Products As p Where p.Category.CategoryName = 'Beverages'";

                using (EntityDataReader reader = cmd.ExecuteReader(CommandBehavior.SequentialAccess))
                {
                    List<Product> productsList = new List<Product>();
                    while (reader.Read())
                    {
                        DbDataRecord record = (DbDataRecord)reader.GetValue(0);

                        // 'materialize' the product by accessing each field and value. Because we are materializing products, we won't have any nested data readers or records.
                        int fieldCount = record.FieldCount;

                        // Treat all products as Product, even if they are the subtype DiscontinuedProduct.
                        Product product = new Product();  

                        product.ProductID = record.GetInt32(0);
                        product.ProductName = record.GetString(1);
                        product.SupplierID = record.GetInt32(2);
                        product.CategoryID = record.GetInt32(3);
                        product.QuantityPerUnit = record.GetString(4);
                        product.UnitPrice = record.GetDecimal(5);
                        product.UnitsInStock = record.GetInt16(6);
                        product.UnitsOnOrder = record.GetInt16(7);
                        product.ReorderLevel = record.GetInt16(8);
                        product.Discontinued = record.GetBoolean(9);

                        productsList.Add(product);
                    }
                }
            }
        }

        public void ExecuteStoreQuery()
        {
            using (NorthwindEntities context = new NorthwindEntities())
            {
                ObjectResult<Product> beverages = context.ExecuteStoreQuery<Product>(
@"    SELECT        P.ProductID, P.ProductName, P.SupplierID, P.CategoryID, P.QuantityPerUnit, P.UnitPrice, P.UnitsInStock, P.UnitsOnOrder, P.ReorderLevel, P.Discontinued
    FROM            Products AS P INNER JOIN Categories AS C ON P.CategoryID = C.CategoryID
    WHERE        (C.CategoryName = 'Beverages')"
);
                beverages.ToList();
            }
        }

        public void ExecuteStoreQueryDbContext()
        {
            using (var context = new QueryComparison.DbC.NorthwindEntities())
            {
                var beverages = context.Database.SqlQuery\<QueryComparison.DbC.Product>(
@"    SELECT        P.ProductID, P.ProductName, P.SupplierID, P.CategoryID, P.QuantityPerUnit, P.UnitPrice, P.UnitsInStock, P.UnitsOnOrder, P.ReorderLevel, P.Discontinued
    FROM            Products AS P INNER JOIN Categories AS C ON P.CategoryID = C.CategoryID
    WHERE        (C.CategoryName = 'Beverages')"
);
                beverages.ToList();
            }
        }

        public void ExecuteStoreQueryDbSet()
        {
            using (var context = new QueryComparison.DbC.NorthwindEntities())
            {
                var beverages = context.Products.SqlQuery(
@"    SELECT        P.ProductID, P.ProductName, P.SupplierID, P.CategoryID, P.QuantityPerUnit, P.UnitPrice, P.UnitsInStock, P.UnitsOnOrder, P.ReorderLevel, P.Discontinued
    FROM            Products AS P INNER JOIN Categories AS C ON P.CategoryID = C.CategoryID
    WHERE        (C.CategoryName = 'Beverages')"
);
                beverages.ToList();
            }
        }

        public void LINQIncludingContextCreationDbContext()
        {
            using (var context = new QueryComparison.DbC.NorthwindEntities())
            {                 
                var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
                q.ToList();
            }
        }

        public void LINQNoTrackingDbContext()
        {
            using (var context = new QueryComparison.DbC.NorthwindEntities())
            {
                var q = context.Products.AsNoTracking().Where(p => p.Category.CategoryName == "Beverages");
                q.ToList();
            }
        }
    }
}
```

### <a name="113-c-navision-model"></a><span data-ttu-id="eeb91-1075">11.3 C.Navision 模型</span><span class="sxs-lookup"><span data-stu-id="eeb91-1075">11.3 C. Navision Model</span></span>

<span data-ttu-id="eeb91-1076">Navision 資料庫是用來示範 Microsoft Dynamics – 導覽大型資料庫</span><span class="sxs-lookup"><span data-stu-id="eeb91-1076">The Navision database is a large database used to demo Microsoft Dynamics – NAV.</span></span> <span data-ttu-id="eeb91-1077">產生的概念模型包含 1005年實體集和 4227 關聯集。</span><span class="sxs-lookup"><span data-stu-id="eeb91-1077">The generated conceptual model contains 1005 entity sets and 4227 association sets.</span></span> <span data-ttu-id="eeb91-1078">在測試中使用的模型是 「 一般 」 – 沒有繼承已新增給它。</span><span class="sxs-lookup"><span data-stu-id="eeb91-1078">The model used in the test is “flat” – no inheritance has been added to it.</span></span>

#### <a name="1131-queries-used-for-navision-tests"></a><span data-ttu-id="eeb91-1079">11.3.1 查詢用於 Navision 測試</span><span class="sxs-lookup"><span data-stu-id="eeb91-1079">11.3.1 Queries used for Navision tests</span></span>

<span data-ttu-id="eeb91-1080">搭配 Navision 模型的 [查詢] 清單會包含 3 個類別的 Entity SQL 查詢：</span><span class="sxs-lookup"><span data-stu-id="eeb91-1080">The queries list used with the Navision model contains 3 categories of Entity SQL queries:</span></span>

##### <a name="11311-lookup"></a><span data-ttu-id="eeb91-1081">11.3.1.1 查閱</span><span class="sxs-lookup"><span data-stu-id="eeb91-1081">11.3.1.1 Lookup</span></span>

<span data-ttu-id="eeb91-1082">簡單的查閱查詢沒有彙總</span><span class="sxs-lookup"><span data-stu-id="eeb91-1082">A simple lookup query with no aggregations</span></span>

-   <span data-ttu-id="eeb91-1083">計數： 16232</span><span class="sxs-lookup"><span data-stu-id="eeb91-1083">Count: 16232</span></span>
-   <span data-ttu-id="eeb91-1084">範例：</span><span class="sxs-lookup"><span data-stu-id="eeb91-1084">Example:</span></span>

``` xml
  <Query complexity="Lookup">
    <CommandText>Select value distinct top(4) e.Idle_Time From NavisionFKContext.Session as e</CommandText>
  </Query>
```

##### <a name="11312-singleaggregating"></a><span data-ttu-id="eeb91-1085">11.3.1.2 SingleAggregating</span><span class="sxs-lookup"><span data-stu-id="eeb91-1085">11.3.1.2 SingleAggregating</span></span>

<span data-ttu-id="eeb91-1086">一般的 BI 查詢多個彙總，但沒有小計 （單一查詢）</span><span class="sxs-lookup"><span data-stu-id="eeb91-1086">A normal BI query with multiple aggregations, but no subtotals (single query)</span></span>

-   <span data-ttu-id="eeb91-1087">計數： 2313年</span><span class="sxs-lookup"><span data-stu-id="eeb91-1087">Count: 2313</span></span>
-   <span data-ttu-id="eeb91-1088">範例：</span><span class="sxs-lookup"><span data-stu-id="eeb91-1088">Example:</span></span>

``` xml
  <Query complexity="SingleAggregating">
    <CommandText>NavisionFK.MDF_SessionLogin_Time_Max()</CommandText>
  </Query>
```

<span data-ttu-id="eeb91-1089">其中 MDF\_SessionLogin\_時間\_max （） 做為模型中所定義：</span><span class="sxs-lookup"><span data-stu-id="eeb91-1089">Where MDF\_SessionLogin\_Time\_Max() is defined in the model as:</span></span>

``` xml
  <Function Name="MDF_SessionLogin_Time_Max" ReturnType="Collection(DateTime)">
    <DefiningExpression>SELECT VALUE Edm.Min(E.Login_Time) FROM NavisionFKContext.Session as E</DefiningExpression>
  </Function>
```

##### <a name="11313-aggregatingsubtotals"></a><span data-ttu-id="eeb91-1090">11.3.1.3 AggregatingSubtotals</span><span class="sxs-lookup"><span data-stu-id="eeb91-1090">11.3.1.3 AggregatingSubtotals</span></span>

<span data-ttu-id="eeb91-1091">BI 查詢與彙總小計 （透過所有的聯集）</span><span class="sxs-lookup"><span data-stu-id="eeb91-1091">A BI query with aggregations and subtotals (via union all)</span></span>

-   <span data-ttu-id="eeb91-1092">計數： 178</span><span class="sxs-lookup"><span data-stu-id="eeb91-1092">Count: 178</span></span>
-   <span data-ttu-id="eeb91-1093">範例：</span><span class="sxs-lookup"><span data-stu-id="eeb91-1093">Example:</span></span>

``` xml
  <Query complexity="AggregatingSubtotals">
    <CommandText>
using NavisionFK;
function AmountConsumed(entities Collection([CRONUS_International_Ltd__Zone])) as
(
    Edm.Sum(select value N.Block_Movement FROM entities as E, E.CRONUS_International_Ltd__Bin as N)
)
function AmountConsumed(P1 Edm.Int32) as
(
    AmountConsumed(select value e from NavisionFKContext.CRONUS_International_Ltd__Zone as e where e.Zone_Ranking = P1)
)
----------------------------------------------------------------------------------------------------------------------
(
    select top(10) Zone_Ranking, Cross_Dock_Bin_Zone, AmountConsumed(GroupPartition(E))
    from NavisionFKContext.CRONUS_International_Ltd__Zone as E
    where AmountConsumed(E.Zone_Ranking) > @MinAmountConsumed
    group by E.Zone_Ranking, E.Cross_Dock_Bin_Zone
)
union all
(
    select top(10) Zone_Ranking, Cast(null as Edm.Byte) as P2, AmountConsumed(GroupPartition(E))
    from NavisionFKContext.CRONUS_International_Ltd__Zone as E
    where AmountConsumed(E.Zone_Ranking) > @MinAmountConsumed
    group by E.Zone_Ranking
)
union all
{
    Row(Cast(null as Edm.Int32) as P1, Cast(null as Edm.Byte) as P2, AmountConsumed(select value E
                                                                         from NavisionFKContext.CRONUS_International_Ltd__Zone as E
                                                                         where AmountConsumed(E.Zone_Ranking) > @MinAmountConsumed))
}</CommandText>
    <Parameters>
      <Parameter Name="MinAmountConsumed" DbType="Int32" Value="10000" />
    </Parameters>
  </Query>
```
