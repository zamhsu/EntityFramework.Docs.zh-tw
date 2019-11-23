---
title: EF4、EF5 和 EF6 的效能考慮-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: d6d5a465-6434-45fa-855d-5eb48c61a2ea
ms.openlocfilehash: 07eb605f0d39f0c1bcfe781540525180f0dd0b22
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72181679"
---
# <a name="performance-considerations-for-ef-4-5-and-6"></a><span data-ttu-id="820d5-102">EF 4、5和6的效能考慮</span><span class="sxs-lookup"><span data-stu-id="820d5-102">Performance considerations for EF 4, 5, and 6</span></span>
<span data-ttu-id="820d5-103">依 David Obando、Eric Dettinger 及其他</span><span class="sxs-lookup"><span data-stu-id="820d5-103">By David Obando, Eric Dettinger and others</span></span>

<span data-ttu-id="820d5-104">發行日期：2012年4月</span><span class="sxs-lookup"><span data-stu-id="820d5-104">Published: April 2012</span></span>

<span data-ttu-id="820d5-105">上次更新日期：5月2014</span><span class="sxs-lookup"><span data-stu-id="820d5-105">Last updated: May 2014</span></span>

------------------------------------------------------------------------

## <a name="1-introduction"></a><span data-ttu-id="820d5-106">1. 簡介</span><span class="sxs-lookup"><span data-stu-id="820d5-106">1. Introduction</span></span>

<span data-ttu-id="820d5-107">物件關聯式對應架構是一個便利的方式，可在物件導向應用程式中提供資料存取的抽象概念。</span><span class="sxs-lookup"><span data-stu-id="820d5-107">Object-Relational Mapping frameworks are a convenient way to provide an abstraction for data access in an object-oriented application.</span></span> <span data-ttu-id="820d5-108">針對 .NET 應用程式，Microsoft 建議的 O/RM Entity Framework。</span><span class="sxs-lookup"><span data-stu-id="820d5-108">For .NET applications, Microsoft's recommended O/RM is Entity Framework.</span></span> <span data-ttu-id="820d5-109">不過，透過任何抽象概念，效能可能會成為一大顧慮。</span><span class="sxs-lookup"><span data-stu-id="820d5-109">With any abstraction though, performance can become a concern.</span></span>

<span data-ttu-id="820d5-110">本白皮書的撰寫目的是要示範使用 Entity Framework 開發應用程式時的效能考慮，讓開發人員瞭解可能會影響效能的 Entity Framework 內部演算法，以及提供調查秘訣和改善使用 Entity Framework 之應用程式的效能。</span><span class="sxs-lookup"><span data-stu-id="820d5-110">This whitepaper was written to show the performance considerations when developing applications using Entity Framework, to give developers an idea of the Entity Framework internal algorithms that can affect performance, and to provide tips for investigation and improving performance in their applications that use Entity Framework.</span></span> <span data-ttu-id="820d5-111">有一些很好的主題可瞭解網路上已有的效能，我們也嘗試盡可能指向這些資源。</span><span class="sxs-lookup"><span data-stu-id="820d5-111">There are a number of good topics on performance already available on the web, and we've also tried pointing to these resources where possible.</span></span>

<span data-ttu-id="820d5-112">效能是一種棘手的主題。</span><span class="sxs-lookup"><span data-stu-id="820d5-112">Performance is a tricky topic.</span></span> <span data-ttu-id="820d5-113">本白皮書旨在協助您針對使用 Entity Framework 的應用程式，做出與效能相關的決策。</span><span class="sxs-lookup"><span data-stu-id="820d5-113">This whitepaper is intended as a resource to help you make performance related decisions for your applications that use Entity Framework.</span></span> <span data-ttu-id="820d5-114">我們已包含一些測試度量來示範效能，但這些計量並不是要做為您在應用程式中所看到效能的絕對指標。</span><span class="sxs-lookup"><span data-stu-id="820d5-114">We have included some test metrics to demonstrate performance, but these metrics aren't intended as absolute indicators of the performance you will see in your application.</span></span>

<span data-ttu-id="820d5-115">基於實際目的，本檔假設 Entity Framework 4 是在 .NET 4.0 之下執行，而 Entity Framework 5 和6則是在 .NET 4.5 之下執行。</span><span class="sxs-lookup"><span data-stu-id="820d5-115">For practical purposes, this document assumes Entity Framework 4 is run under .NET 4.0 and Entity Framework 5 and 6 are run under .NET 4.5.</span></span> <span data-ttu-id="820d5-116">Entity Framework 5 所做的許多效能改進，都位於 .NET 4.5 隨附的核心元件中。</span><span class="sxs-lookup"><span data-stu-id="820d5-116">Many of the performance improvements made for Entity Framework 5 reside within the core components that ship with .NET 4.5.</span></span>

<span data-ttu-id="820d5-117">Entity Framework 6 是頻外版本，不依賴 .NET 隨附的 Entity Framework 元件。</span><span class="sxs-lookup"><span data-stu-id="820d5-117">Entity Framework 6 is an out of band release and does not depend on the Entity Framework components that ship with .NET.</span></span> <span data-ttu-id="820d5-118">Entity Framework 6 適用于 .NET 4.0 和 .NET 4.5，而且可以為尚未從 .NET 4.0 升級，但想要在應用程式中使用最新 Entity Framework 位的使用者提供極大的效能優勢。</span><span class="sxs-lookup"><span data-stu-id="820d5-118">Entity Framework 6 work on both .NET 4.0 and .NET 4.5, and can offer a big performance benefit to those who haven’t upgraded from .NET 4.0 but want the latest Entity Framework bits in their application.</span></span> <span data-ttu-id="820d5-119">當本檔提及 Entity Framework 6 時，它是指在撰寫本文時可用的最新版本：版本6.1.0。</span><span class="sxs-lookup"><span data-stu-id="820d5-119">When this document mentions Entity Framework 6, it refers to the latest version available at the time of this writing: version 6.1.0.</span></span>

## <a name="2-cold-vs-warm-query-execution"></a><span data-ttu-id="820d5-120">2. 冷與暖查詢執行</span><span class="sxs-lookup"><span data-stu-id="820d5-120">2. Cold vs. Warm Query Execution</span></span>

<span data-ttu-id="820d5-121">第一次針對指定的模型進行任何查詢時，Entity Framework 會在幕後執行許多工作，以載入和驗證模型。</span><span class="sxs-lookup"><span data-stu-id="820d5-121">The very first time any query is made against a given model, the Entity Framework does a lot of work behind the scenes to load and validate the model.</span></span> <span data-ttu-id="820d5-122">我們經常將這個第一個查詢稱為「冷」查詢。</span><span class="sxs-lookup"><span data-stu-id="820d5-122">We frequently refer to this first query as a "cold" query.</span></span><span data-ttu-id="820d5-123">  針對已載入的模型進行進一步的查詢稱為「暖」查詢，而且速度更快。</span><span class="sxs-lookup"><span data-stu-id="820d5-123">  Further queries against an already loaded model are known as "warm" queries, and are much faster.</span></span>

<span data-ttu-id="820d5-124">讓我們看看使用 Entity Framework 執行查詢時所花費時間的高階觀點，並查看 Entity Framework 6 中的專案的改善程度。</span><span class="sxs-lookup"><span data-stu-id="820d5-124">Let’s take a high-level view of where time is spent when executing a query using Entity Framework, and see where things are improving in Entity Framework 6.</span></span>

<span data-ttu-id="820d5-125">**第一個查詢執行–冷查詢**</span><span class="sxs-lookup"><span data-stu-id="820d5-125">**First Query Execution – cold query**</span></span>

| <span data-ttu-id="820d5-126">程式碼使用者寫入</span><span class="sxs-lookup"><span data-stu-id="820d5-126">Code User Writes</span></span>                                                                                     | <span data-ttu-id="820d5-127">動作</span><span class="sxs-lookup"><span data-stu-id="820d5-127">Action</span></span>                    | <span data-ttu-id="820d5-128">EF4 效能影響</span><span class="sxs-lookup"><span data-stu-id="820d5-128">EF4 Performance Impact</span></span>                                                                                                                                                                                                                                                                                                                                                                                                        | <span data-ttu-id="820d5-129">EF5 效能影響</span><span class="sxs-lookup"><span data-stu-id="820d5-129">EF5 Performance Impact</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                    | <span data-ttu-id="820d5-130">EF6 效能影響</span><span class="sxs-lookup"><span data-stu-id="820d5-130">EF6 Performance Impact</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
|:-----------------------------------------------------------------------------------------------------|:--------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `using(var db = new MyContext())` <br/> `{`                                                          | <span data-ttu-id="820d5-131">內容建立</span><span class="sxs-lookup"><span data-stu-id="820d5-131">Context creation</span></span>          | <span data-ttu-id="820d5-132">中等</span><span class="sxs-lookup"><span data-stu-id="820d5-132">Medium</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                        | <span data-ttu-id="820d5-133">中等</span><span class="sxs-lookup"><span data-stu-id="820d5-133">Medium</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | <span data-ttu-id="820d5-134">低</span><span class="sxs-lookup"><span data-stu-id="820d5-134">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `  var q1 = ` <br/> `    from c in db.Customers` <br/> `    where c.Id == id1` <br/> `    select c;` | <span data-ttu-id="820d5-135">查詢運算式建立</span><span class="sxs-lookup"><span data-stu-id="820d5-135">Query expression creation</span></span> | <span data-ttu-id="820d5-136">低</span><span class="sxs-lookup"><span data-stu-id="820d5-136">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                           | <span data-ttu-id="820d5-137">低</span><span class="sxs-lookup"><span data-stu-id="820d5-137">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="820d5-138">低</span><span class="sxs-lookup"><span data-stu-id="820d5-138">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `  var c1 = q1.First();`                                                                             | <span data-ttu-id="820d5-139">LINQ 查詢執行</span><span class="sxs-lookup"><span data-stu-id="820d5-139">LINQ query execution</span></span>      | <span data-ttu-id="820d5-140">-中繼資料載入：高但快取</span><span class="sxs-lookup"><span data-stu-id="820d5-140">- Metadata loading: High but cached</span></span> <br/> <span data-ttu-id="820d5-141">-View 世代：可能非常高，但已快取</span><span class="sxs-lookup"><span data-stu-id="820d5-141">- View generation: Potentially very high but cached</span></span> <br/> <span data-ttu-id="820d5-142">-參數評估：中</span><span class="sxs-lookup"><span data-stu-id="820d5-142">- Parameter evaluation: Medium</span></span> <br/> <span data-ttu-id="820d5-143">-查詢轉譯：中</span><span class="sxs-lookup"><span data-stu-id="820d5-143">- Query translation: Medium</span></span> <br/> <span data-ttu-id="820d5-144">-具體化程式產生：中但快取</span><span class="sxs-lookup"><span data-stu-id="820d5-144">- Materializer generation: Medium but cached</span></span> <br/> <span data-ttu-id="820d5-145">-資料庫查詢執行：可能很高</span><span class="sxs-lookup"><span data-stu-id="820d5-145">- Database query execution: Potentially high</span></span> <br/> <span data-ttu-id="820d5-146">+ 連接。開啟</span><span class="sxs-lookup"><span data-stu-id="820d5-146">+ Connection.Open</span></span> <br/> <span data-ttu-id="820d5-147">+ 命令。 ExecuteReader</span><span class="sxs-lookup"><span data-stu-id="820d5-147">+ Command.ExecuteReader</span></span> <br/> <span data-ttu-id="820d5-148">+ DataReader. 讀取</span><span class="sxs-lookup"><span data-stu-id="820d5-148">+ DataReader.Read</span></span> <br/> <span data-ttu-id="820d5-149">物件具體化：中</span><span class="sxs-lookup"><span data-stu-id="820d5-149">Object materialization: Medium</span></span> <br/> <span data-ttu-id="820d5-150">-身分識別查閱：中</span><span class="sxs-lookup"><span data-stu-id="820d5-150">- Identity lookup: Medium</span></span> | <span data-ttu-id="820d5-151">-中繼資料載入：高但快取</span><span class="sxs-lookup"><span data-stu-id="820d5-151">- Metadata loading: High but cached</span></span> <br/> <span data-ttu-id="820d5-152">-View 世代：可能非常高，但已快取</span><span class="sxs-lookup"><span data-stu-id="820d5-152">- View generation: Potentially very high but cached</span></span> <br/> <span data-ttu-id="820d5-153">-參數評估：低</span><span class="sxs-lookup"><span data-stu-id="820d5-153">- Parameter evaluation: Low</span></span> <br/> <span data-ttu-id="820d5-154">-查詢轉譯：中但快取</span><span class="sxs-lookup"><span data-stu-id="820d5-154">- Query translation: Medium but cached</span></span> <br/> <span data-ttu-id="820d5-155">-具體化程式產生：中但快取</span><span class="sxs-lookup"><span data-stu-id="820d5-155">- Materializer generation: Medium but cached</span></span> <br/> <span data-ttu-id="820d5-156">-資料庫查詢執行：可能很高（在某些情況下為較佳的查詢）</span><span class="sxs-lookup"><span data-stu-id="820d5-156">- Database query execution: Potentially high (Better queries in some situations)</span></span> <br/> <span data-ttu-id="820d5-157">+ 連接。開啟</span><span class="sxs-lookup"><span data-stu-id="820d5-157">+ Connection.Open</span></span> <br/> <span data-ttu-id="820d5-158">+ 命令。 ExecuteReader</span><span class="sxs-lookup"><span data-stu-id="820d5-158">+ Command.ExecuteReader</span></span> <br/> <span data-ttu-id="820d5-159">+ DataReader. 讀取</span><span class="sxs-lookup"><span data-stu-id="820d5-159">+ DataReader.Read</span></span> <br/> <span data-ttu-id="820d5-160">物件具體化：中</span><span class="sxs-lookup"><span data-stu-id="820d5-160">Object materialization: Medium</span></span> <br/> <span data-ttu-id="820d5-161">-身分識別查閱：中</span><span class="sxs-lookup"><span data-stu-id="820d5-161">- Identity lookup: Medium</span></span> | <span data-ttu-id="820d5-162">-中繼資料載入：高但快取</span><span class="sxs-lookup"><span data-stu-id="820d5-162">- Metadata loading: High but cached</span></span> <br/> <span data-ttu-id="820d5-163">-視圖產生：中但快取</span><span class="sxs-lookup"><span data-stu-id="820d5-163">- View generation: Medium but cached</span></span> <br/> <span data-ttu-id="820d5-164">-參數評估：低</span><span class="sxs-lookup"><span data-stu-id="820d5-164">- Parameter evaluation: Low</span></span> <br/> <span data-ttu-id="820d5-165">-查詢轉譯：中但快取</span><span class="sxs-lookup"><span data-stu-id="820d5-165">- Query translation: Medium but cached</span></span> <br/> <span data-ttu-id="820d5-166">-具體化程式產生：中但快取</span><span class="sxs-lookup"><span data-stu-id="820d5-166">- Materializer generation: Medium but cached</span></span> <br/> <span data-ttu-id="820d5-167">-資料庫查詢執行：可能很高（在某些情況下為較佳的查詢）</span><span class="sxs-lookup"><span data-stu-id="820d5-167">- Database query execution: Potentially high (Better queries in some situations)</span></span> <br/> <span data-ttu-id="820d5-168">+ 連接。開啟</span><span class="sxs-lookup"><span data-stu-id="820d5-168">+ Connection.Open</span></span> <br/> <span data-ttu-id="820d5-169">+ 命令。 ExecuteReader</span><span class="sxs-lookup"><span data-stu-id="820d5-169">+ Command.ExecuteReader</span></span> <br/> <span data-ttu-id="820d5-170">+ DataReader. 讀取</span><span class="sxs-lookup"><span data-stu-id="820d5-170">+ DataReader.Read</span></span> <br/> <span data-ttu-id="820d5-171">物件具體化：中（速度比 EF5 快）</span><span class="sxs-lookup"><span data-stu-id="820d5-171">Object materialization: Medium (Faster than EF5)</span></span> <br/> <span data-ttu-id="820d5-172">-身分識別查閱：中</span><span class="sxs-lookup"><span data-stu-id="820d5-172">- Identity lookup: Medium</span></span> |
| `}`                                                                                                  | <span data-ttu-id="820d5-173">連接。關閉</span><span class="sxs-lookup"><span data-stu-id="820d5-173">Connection.Close</span></span>          | <span data-ttu-id="820d5-174">低</span><span class="sxs-lookup"><span data-stu-id="820d5-174">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                           | <span data-ttu-id="820d5-175">低</span><span class="sxs-lookup"><span data-stu-id="820d5-175">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="820d5-176">低</span><span class="sxs-lookup"><span data-stu-id="820d5-176">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |


<span data-ttu-id="820d5-177">**第二個查詢執行–暖查詢**</span><span class="sxs-lookup"><span data-stu-id="820d5-177">**Second Query Execution – warm query**</span></span>

| <span data-ttu-id="820d5-178">程式碼使用者寫入</span><span class="sxs-lookup"><span data-stu-id="820d5-178">Code User Writes</span></span>                                                                                     | <span data-ttu-id="820d5-179">動作</span><span class="sxs-lookup"><span data-stu-id="820d5-179">Action</span></span>                    | <span data-ttu-id="820d5-180">EF4 效能影響</span><span class="sxs-lookup"><span data-stu-id="820d5-180">EF4 Performance Impact</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | <span data-ttu-id="820d5-181">EF5 效能影響</span><span class="sxs-lookup"><span data-stu-id="820d5-181">EF5 Performance Impact</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | <span data-ttu-id="820d5-182">EF6 效能影響</span><span class="sxs-lookup"><span data-stu-id="820d5-182">EF6 Performance Impact</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
|:-----------------------------------------------------------------------------------------------------|:--------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `using(var db = new MyContext())` <br/> `{`                                                          | <span data-ttu-id="820d5-183">內容建立</span><span class="sxs-lookup"><span data-stu-id="820d5-183">Context creation</span></span>          | <span data-ttu-id="820d5-184">中等</span><span class="sxs-lookup"><span data-stu-id="820d5-184">Medium</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | <span data-ttu-id="820d5-185">中等</span><span class="sxs-lookup"><span data-stu-id="820d5-185">Medium</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | <span data-ttu-id="820d5-186">低</span><span class="sxs-lookup"><span data-stu-id="820d5-186">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `  var q1 = ` <br/> `    from c in db.Customers` <br/> `    where c.Id == id1` <br/> `    select c;` | <span data-ttu-id="820d5-187">查詢運算式建立</span><span class="sxs-lookup"><span data-stu-id="820d5-187">Query expression creation</span></span> | <span data-ttu-id="820d5-188">低</span><span class="sxs-lookup"><span data-stu-id="820d5-188">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | <span data-ttu-id="820d5-189">低</span><span class="sxs-lookup"><span data-stu-id="820d5-189">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | <span data-ttu-id="820d5-190">低</span><span class="sxs-lookup"><span data-stu-id="820d5-190">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `  var c1 = q1.First();`                                                                             | <span data-ttu-id="820d5-191">LINQ 查詢執行</span><span class="sxs-lookup"><span data-stu-id="820d5-191">LINQ query execution</span></span>      | <span data-ttu-id="820d5-192">-中繼資料~~載入~~查閱：~~高但快~~取低</span><span class="sxs-lookup"><span data-stu-id="820d5-192">- Metadata ~~loading~~ lookup: ~~High but cached~~ Low</span></span> <br/> <span data-ttu-id="820d5-193">-View~~世代~~查閱：~~可能非常高，但快~~取不足</span><span class="sxs-lookup"><span data-stu-id="820d5-193">- View ~~generation~~ lookup: ~~Potentially very high but cached~~ Low</span></span> <br/> <span data-ttu-id="820d5-194">-參數評估：中</span><span class="sxs-lookup"><span data-stu-id="820d5-194">- Parameter evaluation: Medium</span></span> <br/> <span data-ttu-id="820d5-195">-查詢~~轉譯~~查閱：中</span><span class="sxs-lookup"><span data-stu-id="820d5-195">- Query ~~translation~~ lookup: Medium</span></span> <br/> <span data-ttu-id="820d5-196">-具體化程式~~產生~~查閱：~~中等但~~快取偏低</span><span class="sxs-lookup"><span data-stu-id="820d5-196">- Materializer ~~generation~~ lookup: ~~Medium but cached~~ Low</span></span> <br/> <span data-ttu-id="820d5-197">-資料庫查詢執行：可能很高</span><span class="sxs-lookup"><span data-stu-id="820d5-197">- Database query execution: Potentially high</span></span> <br/> <span data-ttu-id="820d5-198">+ 連接。開啟</span><span class="sxs-lookup"><span data-stu-id="820d5-198">+ Connection.Open</span></span> <br/> <span data-ttu-id="820d5-199">+ 命令。 ExecuteReader</span><span class="sxs-lookup"><span data-stu-id="820d5-199">+ Command.ExecuteReader</span></span> <br/> <span data-ttu-id="820d5-200">+ DataReader. 讀取</span><span class="sxs-lookup"><span data-stu-id="820d5-200">+ DataReader.Read</span></span> <br/> <span data-ttu-id="820d5-201">物件具體化：中</span><span class="sxs-lookup"><span data-stu-id="820d5-201">Object materialization: Medium</span></span> <br/> <span data-ttu-id="820d5-202">-身分識別查閱：中</span><span class="sxs-lookup"><span data-stu-id="820d5-202">- Identity lookup: Medium</span></span> | <span data-ttu-id="820d5-203">-中繼資料~~載入~~查閱：~~高但快~~取低</span><span class="sxs-lookup"><span data-stu-id="820d5-203">- Metadata ~~loading~~ lookup: ~~High but cached~~ Low</span></span> <br/> <span data-ttu-id="820d5-204">-View~~世代~~查閱：~~可能非常高，但快~~取不足</span><span class="sxs-lookup"><span data-stu-id="820d5-204">- View ~~generation~~ lookup: ~~Potentially very high but cached~~ Low</span></span> <br/> <span data-ttu-id="820d5-205">-參數評估：低</span><span class="sxs-lookup"><span data-stu-id="820d5-205">- Parameter evaluation: Low</span></span> <br/> <span data-ttu-id="820d5-206">-查詢~~轉譯~~查閱：~~中但~~快取偏低</span><span class="sxs-lookup"><span data-stu-id="820d5-206">- Query ~~translation~~ lookup: ~~Medium but cached~~ Low</span></span> <br/> <span data-ttu-id="820d5-207">-具體化程式~~產生~~查閱：~~中等但~~快取偏低</span><span class="sxs-lookup"><span data-stu-id="820d5-207">- Materializer ~~generation~~ lookup: ~~Medium but cached~~ Low</span></span> <br/> <span data-ttu-id="820d5-208">-資料庫查詢執行：可能很高（在某些情況下為較佳的查詢）</span><span class="sxs-lookup"><span data-stu-id="820d5-208">- Database query execution: Potentially high (Better queries in some situations)</span></span> <br/> <span data-ttu-id="820d5-209">+ 連接。開啟</span><span class="sxs-lookup"><span data-stu-id="820d5-209">+ Connection.Open</span></span> <br/> <span data-ttu-id="820d5-210">+ 命令。 ExecuteReader</span><span class="sxs-lookup"><span data-stu-id="820d5-210">+ Command.ExecuteReader</span></span> <br/> <span data-ttu-id="820d5-211">+ DataReader. 讀取</span><span class="sxs-lookup"><span data-stu-id="820d5-211">+ DataReader.Read</span></span> <br/> <span data-ttu-id="820d5-212">物件具體化：中</span><span class="sxs-lookup"><span data-stu-id="820d5-212">Object materialization: Medium</span></span> <br/> <span data-ttu-id="820d5-213">-身分識別查閱：中</span><span class="sxs-lookup"><span data-stu-id="820d5-213">- Identity lookup: Medium</span></span> | <span data-ttu-id="820d5-214">-中繼資料~~載入~~查閱：~~高但快~~取低</span><span class="sxs-lookup"><span data-stu-id="820d5-214">- Metadata ~~loading~~ lookup: ~~High but cached~~ Low</span></span> <br/> <span data-ttu-id="820d5-215">-View~~世代~~查閱：~~中但~~快取偏低</span><span class="sxs-lookup"><span data-stu-id="820d5-215">- View ~~generation~~ lookup: ~~Medium but cached~~ Low</span></span> <br/> <span data-ttu-id="820d5-216">-參數評估：低</span><span class="sxs-lookup"><span data-stu-id="820d5-216">- Parameter evaluation: Low</span></span> <br/> <span data-ttu-id="820d5-217">-查詢~~轉譯~~查閱：~~中但~~快取偏低</span><span class="sxs-lookup"><span data-stu-id="820d5-217">- Query ~~translation~~ lookup: ~~Medium but cached~~ Low</span></span> <br/> <span data-ttu-id="820d5-218">-具體化程式~~產生~~查閱：~~中等但~~快取偏低</span><span class="sxs-lookup"><span data-stu-id="820d5-218">- Materializer ~~generation~~ lookup: ~~Medium but cached~~ Low</span></span> <br/> <span data-ttu-id="820d5-219">-資料庫查詢執行：可能很高（在某些情況下為較佳的查詢）</span><span class="sxs-lookup"><span data-stu-id="820d5-219">- Database query execution: Potentially high (Better queries in some situations)</span></span> <br/> <span data-ttu-id="820d5-220">+ 連接。開啟</span><span class="sxs-lookup"><span data-stu-id="820d5-220">+ Connection.Open</span></span> <br/> <span data-ttu-id="820d5-221">+ 命令。 ExecuteReader</span><span class="sxs-lookup"><span data-stu-id="820d5-221">+ Command.ExecuteReader</span></span> <br/> <span data-ttu-id="820d5-222">+ DataReader. 讀取</span><span class="sxs-lookup"><span data-stu-id="820d5-222">+ DataReader.Read</span></span> <br/> <span data-ttu-id="820d5-223">物件具體化：中（速度比 EF5 快）</span><span class="sxs-lookup"><span data-stu-id="820d5-223">Object materialization: Medium (Faster than EF5)</span></span> <br/> <span data-ttu-id="820d5-224">-身分識別查閱：中</span><span class="sxs-lookup"><span data-stu-id="820d5-224">- Identity lookup: Medium</span></span> |
| `}`                                                                                                  | <span data-ttu-id="820d5-225">連接。關閉</span><span class="sxs-lookup"><span data-stu-id="820d5-225">Connection.Close</span></span>          | <span data-ttu-id="820d5-226">低</span><span class="sxs-lookup"><span data-stu-id="820d5-226">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | <span data-ttu-id="820d5-227">低</span><span class="sxs-lookup"><span data-stu-id="820d5-227">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | <span data-ttu-id="820d5-228">低</span><span class="sxs-lookup"><span data-stu-id="820d5-228">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |


<span data-ttu-id="820d5-229">有數種方式可降低冷和暖查詢的效能成本，我們將在下一節中討論這些功能。</span><span class="sxs-lookup"><span data-stu-id="820d5-229">There are several ways to reduce the performance cost of both cold and warm queries, and we'll take a look at these in the following section.</span></span> <span data-ttu-id="820d5-230">具體而言，我們將探討如何使用預先產生的視圖來降低在冷查詢中載入模型的成本，這應該有助於減輕在視圖產生期間所發生的效能難題。</span><span class="sxs-lookup"><span data-stu-id="820d5-230">Specifically, we'll look at reducing the cost of model loading in cold queries by using pre-generated views, which should help alleviate performance pains experienced during view generation.</span></span> <span data-ttu-id="820d5-231">針對暖查詢，我們將討論查詢計劃快取、沒有追蹤查詢和不同的查詢執行選項。</span><span class="sxs-lookup"><span data-stu-id="820d5-231">For warm queries, we'll cover query plan caching, no tracking queries, and different query execution options.</span></span>

### <a name="21-what-is-view-generation"></a><span data-ttu-id="820d5-232">2.1 什麼是視圖產生？</span><span class="sxs-lookup"><span data-stu-id="820d5-232">2.1 What is View Generation?</span></span>

<span data-ttu-id="820d5-233">為了瞭解什麼是產生視圖，我們必須先瞭解「對應視圖」是什麼。</span><span class="sxs-lookup"><span data-stu-id="820d5-233">In order to understand what view generation is, we must first understand what “Mapping Views” are.</span></span> <span data-ttu-id="820d5-234">對應視圖是對應中針對每個實體集和關聯所指定之轉換的可執行表示。</span><span class="sxs-lookup"><span data-stu-id="820d5-234">Mapping Views are executable representations of the transformations specified in the mapping for each entity set and association.</span></span> <span data-ttu-id="820d5-235">就內部而言，這些對應視圖會採用 CQTs （標準查詢樹狀結構）的形式。</span><span class="sxs-lookup"><span data-stu-id="820d5-235">Internally, these mapping views take the shape of CQTs (canonical query trees).</span></span> <span data-ttu-id="820d5-236">對應視圖有兩種類型：</span><span class="sxs-lookup"><span data-stu-id="820d5-236">There are two types of mapping views:</span></span>

-   <span data-ttu-id="820d5-237">查詢檢視：這些表示從資料庫架構移至概念模型所需的轉換。</span><span class="sxs-lookup"><span data-stu-id="820d5-237">Query views: these represent the transformation necessary to go from the database schema to the conceptual model.</span></span>
-   <span data-ttu-id="820d5-238">更新視圖：這些是從概念模型移至資料庫架構所需的轉換。</span><span class="sxs-lookup"><span data-stu-id="820d5-238">Update views: these represent the transformation necessary to go from the conceptual model to the database schema.</span></span>

<span data-ttu-id="820d5-239">請記住，概念模型可能會以各種不同的方式與資料庫架構不同。</span><span class="sxs-lookup"><span data-stu-id="820d5-239">Keep in mind that the conceptual model might differ from the database schema in various ways.</span></span> <span data-ttu-id="820d5-240">例如，一個單一資料表可能用來儲存兩個不同實體類型的資料。</span><span class="sxs-lookup"><span data-stu-id="820d5-240">For example, one single table might be used to store the data for two different entity types.</span></span> <span data-ttu-id="820d5-241">繼承和非一般對應在對應視圖的複雜性中扮演著角色。</span><span class="sxs-lookup"><span data-stu-id="820d5-241">Inheritance and non-trivial mappings play a role in the complexity of the mapping views.</span></span>

<span data-ttu-id="820d5-242">根據對應規格來計算這些觀點的程式，就是所謂的「視圖產生」。</span><span class="sxs-lookup"><span data-stu-id="820d5-242">The process of computing these views based on the specification of the mapping is what we call view generation.</span></span> <span data-ttu-id="820d5-243">當載入模型時，或在建立時使用「預先產生的視圖」，可以動態地進行視圖產生;後者會以 Entity SQL 語句的形式序列化成 C\# 或 VB 檔案。</span><span class="sxs-lookup"><span data-stu-id="820d5-243">View generation can either take place dynamically when a model is loaded, or at build time, by using "pre-generated views"; the latter are serialized in the form of Entity SQL statements to a C\# or VB file.</span></span>

<span data-ttu-id="820d5-244">當視圖產生時，也會進行驗證。</span><span class="sxs-lookup"><span data-stu-id="820d5-244">When views are generated, they are also validated.</span></span> <span data-ttu-id="820d5-245">從效能的觀點來看，大部分的視圖產生成本實際上都是驗證視圖，這可確保實體之間的連接有意義，並對所有支援的作業具有正確的基數。</span><span class="sxs-lookup"><span data-stu-id="820d5-245">From a performance standpoint, the vast majority of the cost of view generation is actually the validation of the views which ensures that the connections between the entities make sense and have the correct cardinality for all the supported operations.</span></span>

<span data-ttu-id="820d5-246">執行實體集的查詢時，查詢會與對應的查詢檢視結合，而此組合的結果會透過計畫編譯器執行，以建立支援存放區可瞭解的查詢標記法。</span><span class="sxs-lookup"><span data-stu-id="820d5-246">When a query over an entity set is executed, the query is combined with the corresponding query view, and the result of this composition is run through the plan compiler to create the representation of the query that the backing store can understand.</span></span> <span data-ttu-id="820d5-247">針對 SQL Server，此編譯的最終結果會是 T-sql SELECT 語句。</span><span class="sxs-lookup"><span data-stu-id="820d5-247">For SQL Server, the final result of this compilation will be a T-SQL SELECT statement.</span></span> <span data-ttu-id="820d5-248">第一次執行實體集的更新時，更新視圖會透過類似的進程執行，將它轉換成目標資料庫的 DML 語句。</span><span class="sxs-lookup"><span data-stu-id="820d5-248">The first time an update over an entity set is performed, the update view is run through a similar process to transform it into DML statements for the target database.</span></span>

### <a name="22-factors-that-affect-view-generation-performance"></a><span data-ttu-id="820d5-249">影響視圖產生效能的2.2 因素</span><span class="sxs-lookup"><span data-stu-id="820d5-249">2.2 Factors that affect View Generation performance</span></span>

<span data-ttu-id="820d5-250">「視圖產生」步驟的效能不只取決於模型的大小，也會根據模型的互連方式而定。</span><span class="sxs-lookup"><span data-stu-id="820d5-250">The performance of view generation step not only depends on the size of your model but also on how interconnected the model is.</span></span> <span data-ttu-id="820d5-251">如果兩個實體透過繼承鏈或關聯來連線，則稱為「已連接」。</span><span class="sxs-lookup"><span data-stu-id="820d5-251">If two Entities are connected via an inheritance chain or an Association, they are said to be connected.</span></span> <span data-ttu-id="820d5-252">同樣地，如果兩個數據表透過外鍵連接，它們就會連接。</span><span class="sxs-lookup"><span data-stu-id="820d5-252">Similarly if two tables are connected via a foreign key, they are connected.</span></span> <span data-ttu-id="820d5-253">當您的架構中已連線的實體和資料表數目增加時，視圖產生成本就會增加。</span><span class="sxs-lookup"><span data-stu-id="820d5-253">As the number of connected Entities and tables in your schemas increase, the view generation cost increases.</span></span>

<span data-ttu-id="820d5-254">在最差的情況下，我們用來產生和驗證視圖的演算法是指數的，不過我們的確使用一些優化來改善這項功能。</span><span class="sxs-lookup"><span data-stu-id="820d5-254">The algorithm that we use to generate and validate views is exponential in the worst case, though we do use some optimizations to improve this.</span></span> <span data-ttu-id="820d5-255">似乎會對效能造成負面影響的最大因素如下：</span><span class="sxs-lookup"><span data-stu-id="820d5-255">The biggest factors that seem to negatively affect performance are:</span></span>

-   <span data-ttu-id="820d5-256">模型大小，參考實體數目和這些實體之間的關聯量。</span><span class="sxs-lookup"><span data-stu-id="820d5-256">Model size, referring to the number of entities and the amount of associations between these entities.</span></span>
-   <span data-ttu-id="820d5-257">模型複雜度，特別是涉及大量類型的繼承。</span><span class="sxs-lookup"><span data-stu-id="820d5-257">Model complexity, specifically inheritance involving a large number of types.</span></span>
-   <span data-ttu-id="820d5-258">使用獨立關聯，而不是外鍵關聯。</span><span class="sxs-lookup"><span data-stu-id="820d5-258">Using Independent Associations, instead of Foreign Key Associations.</span></span>

<span data-ttu-id="820d5-259">針對小型的簡單模型，成本可能夠小，而不是使用預先產生的視圖。</span><span class="sxs-lookup"><span data-stu-id="820d5-259">For small, simple models the cost may be small enough to not bother using pre-generated views.</span></span> <span data-ttu-id="820d5-260">隨著模型大小和複雜度的增加，有數個選項可用來減少產生和驗證的成本。</span><span class="sxs-lookup"><span data-stu-id="820d5-260">As model size and complexity increase, there are several options available to reduce the cost of view generation and validation.</span></span>

### <a name="23-using-pre-generated-views-to-decrease-model-load-time"></a><span data-ttu-id="820d5-261">2.3 使用預先產生的視圖來減少模型載入時間</span><span class="sxs-lookup"><span data-stu-id="820d5-261">2.3 Using Pre-Generated Views to decrease model load time</span></span>

<span data-ttu-id="820d5-262">如需如何在 Entity Framework 6 上使用預先產生之視圖的詳細資訊，請造訪[預先產生的對應視圖](~/ef6/fundamentals/performance/pre-generated-views.md)</span><span class="sxs-lookup"><span data-stu-id="820d5-262">For detailed information on how to use pre-generated views on Entity Framework 6 visit [Pre-Generated Mapping Views](~/ef6/fundamentals/performance/pre-generated-views.md)</span></span>

#### <a name="231-pre-generated-views-using-the-entity-framework-power-tools-community-edition"></a><span data-ttu-id="820d5-263">使用 Entity Framework Power Tools 社區版2.3.1 預先產生的視圖</span><span class="sxs-lookup"><span data-stu-id="820d5-263">2.3.1 Pre-Generated views using the Entity Framework Power Tools Community Edition</span></span>

<span data-ttu-id="820d5-264">在模型類別檔案上按一下滑鼠右鍵，然後使用 [Entity Framework] 功能表選取 [產生視圖]，即可使用[Entity Framework 6 Power Tool 社區版](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition)來產生 EDMX 和 Code First 模型的查看。</span><span class="sxs-lookup"><span data-stu-id="820d5-264">You can use the [Entity Framework 6 Power Tools Community Edition](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) to generate views of EDMX and Code First models by right-clicking the model class file and using the Entity Framework menu to select “Generate Views”.</span></span> <span data-ttu-id="820d5-265">Entity Framework Power Tools 的社區版僅適用于 DbCoNtext 衍生的內容。</span><span class="sxs-lookup"><span data-stu-id="820d5-265">The Entity Framework Power Tools Community Edition work only on DbContext-derived contexts.</span></span>

#### <a name="232-how-to-use-pre-generated-views-with-a-model-created-by-edmgen"></a><span data-ttu-id="820d5-266">2.3.2 如何搭配 Edmgen.exe 所建立的模型使用預先產生的視圖</span><span class="sxs-lookup"><span data-stu-id="820d5-266">2.3.2 How to use Pre-generated views with a model created by EDMGen</span></span>

<span data-ttu-id="820d5-267">Edmgen.exe 是隨附于 .NET 的公用程式，適用于 Entity Framework 4 和5，但不含 Entity Framework 6。</span><span class="sxs-lookup"><span data-stu-id="820d5-267">EDMGen is a utility that ships with .NET and works with Entity Framework 4 and 5, but not with Entity Framework 6.</span></span> <span data-ttu-id="820d5-268">Edmgen.exe 可讓您從命令列產生模型檔案、物件層和 views。</span><span class="sxs-lookup"><span data-stu-id="820d5-268">EDMGen allows you to generate a model file, the object layer and the views from the command line.</span></span> <span data-ttu-id="820d5-269">其中一個輸出將是您選擇的語言、VB 或 C\#的 Views 檔案。</span><span class="sxs-lookup"><span data-stu-id="820d5-269">One of the outputs will be a Views file in your language of choice, VB or C\#.</span></span> <span data-ttu-id="820d5-270">這是一個程式碼檔案，其中包含每個實體集的 Entity SQL 程式碼片段。</span><span class="sxs-lookup"><span data-stu-id="820d5-270">This is a code file containing Entity SQL snippets for each entity set.</span></span> <span data-ttu-id="820d5-271">若要啟用預先產生的視圖，您只需要在專案中包含檔案。</span><span class="sxs-lookup"><span data-stu-id="820d5-271">To enable pre-generated views, you simply include the file in your project.</span></span>

<span data-ttu-id="820d5-272">如果您手動對模型的架構檔案進行編輯，就必須重新產生 views 檔案。</span><span class="sxs-lookup"><span data-stu-id="820d5-272">If you manually make edits to the schema files for the model, you will need to re-generate the views file.</span></span> <span data-ttu-id="820d5-273">若要這麼做，您可以使用 **/mode： ViewGeneration**旗標執行 edmgen.exe。</span><span class="sxs-lookup"><span data-stu-id="820d5-273">You can do this by running EDMGen with the **/mode:ViewGeneration** flag.</span></span>

#### <a name="233-how-to-use-pre-generated-views-with-an-edmx-file"></a><span data-ttu-id="820d5-274">2.3.3 如何搭配使用預先產生的視圖與 EDMX 檔案</span><span class="sxs-lookup"><span data-stu-id="820d5-274">2.3.3 How to use Pre-Generated Views with an EDMX file</span></span>

<span data-ttu-id="820d5-275">您也可以使用 Edmgen.exe 來產生 EDMX 檔案的 views-先前參考的 MSDN 主題說明如何新增預先建立事件來執行此動作，但這很複雜，而且在某些情況下無法使用。</span><span class="sxs-lookup"><span data-stu-id="820d5-275">You can also use EDMGen to generate views for an EDMX file - the previously referenced MSDN topic describes how to add a pre-build event to do this - but this is complicated and there are some cases where it isn't possible.</span></span> <span data-ttu-id="820d5-276">當您的模型位於 edmx 檔案時，使用 T4 範本來產生視圖通常會比較容易。</span><span class="sxs-lookup"><span data-stu-id="820d5-276">It's generally easier to use a T4 template to generate the views when your model is in an edmx file.</span></span>

<span data-ttu-id="820d5-277">ADO.NET 小組的 blog 有一篇文章，說明如何使用 T4 範本來產生視圖（\<http://blogs.msdn.com/b/adonet/archive/2008/06/20/how-to-use-a-t4-template-for-view-generation.aspx>)。</span><span class="sxs-lookup"><span data-stu-id="820d5-277">The ADO.NET team blog has a post that describes how to use a T4 template for view generation ( \<http://blogs.msdn.com/b/adonet/archive/2008/06/20/how-to-use-a-t4-template-for-view-generation.aspx>).</span></span> <span data-ttu-id="820d5-278">這篇文章包含可供下載並新增至您專案的範本。</span><span class="sxs-lookup"><span data-stu-id="820d5-278">This post includes a template that can be downloaded and added to your project.</span></span> <span data-ttu-id="820d5-279">範本是針對第一個 Entity Framework 版本所撰寫，因此不保證會與最新版本的 Entity Framework 搭配使用。</span><span class="sxs-lookup"><span data-stu-id="820d5-279">The template was written for the first version of Entity Framework, so they aren’t guaranteed to work with the latest versions of Entity Framework.</span></span> <span data-ttu-id="820d5-280">不過，您可以針對 Entity Framework 4 下載更新的視圖產生樣板集，並 5from Visual Studio 資源庫：</span><span class="sxs-lookup"><span data-stu-id="820d5-280">However, you can download a more up-to-date set of view generation templates for Entity Framework 4 and 5from the Visual Studio Gallery:</span></span>

-   <span data-ttu-id="820d5-281">VB.NET： \<http://visualstudiogallery.msdn.microsoft.com/118b44f2-1b91-4de2-a584-7a680418941d></span><span class="sxs-lookup"><span data-stu-id="820d5-281">VB.NET: \<http://visualstudiogallery.msdn.microsoft.com/118b44f2-1b91-4de2-a584-7a680418941d></span></span>
-   <span data-ttu-id="820d5-282">C\#： \<http://visualstudiogallery.msdn.microsoft.com/ae7730ce-ddab-470f-8456-1b313cd2c44d></span><span class="sxs-lookup"><span data-stu-id="820d5-282">C\#: \<http://visualstudiogallery.msdn.microsoft.com/ae7730ce-ddab-470f-8456-1b313cd2c44d></span></span>

<span data-ttu-id="820d5-283">如果您使用 Entity Framework 6，您可以從 \<http://visualstudiogallery.msdn.microsoft.com/18a7db90-6705-4d19-9dd1-0a6c23d0751f>的 Visual Studio 資源庫取得視圖產生 T4 範本。</span><span class="sxs-lookup"><span data-stu-id="820d5-283">If you’re using Entity Framework 6 you can get the view generation T4 templates from the Visual Studio Gallery at \<http://visualstudiogallery.msdn.microsoft.com/18a7db90-6705-4d19-9dd1-0a6c23d0751f>.</span></span>

### <a name="24-reducing-the-cost-of-view-generation"></a><span data-ttu-id="820d5-284">2.4 降低視圖產生成本</span><span class="sxs-lookup"><span data-stu-id="820d5-284">2.4 Reducing the cost of view generation</span></span>

<span data-ttu-id="820d5-285">使用預先產生的視圖，會將產生的視圖成本從模型載入（執行時間）移至設計階段。</span><span class="sxs-lookup"><span data-stu-id="820d5-285">Using pre-generated views moves the cost of view generation from model loading (run time) to design time.</span></span> <span data-ttu-id="820d5-286">雖然這可在執行時間改善啟動效能，但您仍會在開發時遇到產生視圖的困難。</span><span class="sxs-lookup"><span data-stu-id="820d5-286">While this improves startup performance at runtime, you will still experience the pain of view generation while you are developing.</span></span> <span data-ttu-id="820d5-287">還有幾個額外的訣竅，可以在編譯時間和執行時間，協助降低產生視圖的成本。</span><span class="sxs-lookup"><span data-stu-id="820d5-287">There are several additional tricks that can help reduce the cost of view generation, both at compile time and run time.</span></span>

#### <a name="241-using-foreign-key-associations-to-reduce-view-generation-cost"></a><span data-ttu-id="820d5-288">使用外鍵關聯的2.4.1，以降低視圖產生成本</span><span class="sxs-lookup"><span data-stu-id="820d5-288">2.4.1 Using Foreign Key Associations to reduce view generation cost</span></span>

<span data-ttu-id="820d5-289">我們已瞭解將模型中的關聯從獨立關聯切換至外鍵關聯的幾個案例，大幅改善了在視圖產生中所花費的時間。</span><span class="sxs-lookup"><span data-stu-id="820d5-289">We have seen a number of cases where switching the associations in the model from Independent Associations to Foreign Key Associations dramatically improved the time spent in view generation.</span></span>

<span data-ttu-id="820d5-290">為了示範這項改進，我們使用 Edmgen.exe 產生了兩個版本的 Navision 模型。</span><span class="sxs-lookup"><span data-stu-id="820d5-290">To demonstrate this improvement, we generated two versions of the Navision model by using EDMGen.</span></span> <span data-ttu-id="820d5-291">*注意：如需 Navision 模型的說明，請參閱附錄 C。*</span><span class="sxs-lookup"><span data-stu-id="820d5-291">*Note: see appendix C for a description of the Navision model.*</span></span> <span data-ttu-id="820d5-292">Navision 模型在此練習中很有趣，因為它的實體數量和關聯性非常龐大。</span><span class="sxs-lookup"><span data-stu-id="820d5-292">The Navision model is interesting for this exercise due to its very large amount of entities and relationships between them.</span></span>

<span data-ttu-id="820d5-293">這個非常大型模型的其中一個版本是使用外鍵關聯所產生，另一個則是使用獨立關聯產生。</span><span class="sxs-lookup"><span data-stu-id="820d5-293">One version of this very large model was generated with Foreign Keys Associations and the other was generated with Independent Associations.</span></span> <span data-ttu-id="820d5-294">接著，我們會計時為每個模型產生視圖所花費的時間。</span><span class="sxs-lookup"><span data-stu-id="820d5-294">We then timed how long it took to generate the views for each model.</span></span> <span data-ttu-id="820d5-295">Entity Framework 5 測試使用類別 EntityViewGenerator 中的 GenerateViews （）方法來產生視圖，而 Entity Framework 6 測試使用類別 StorageMappingItemCollection 的 GenerateViews （）方法。</span><span class="sxs-lookup"><span data-stu-id="820d5-295">Entity Framework 5 test used the GenerateViews() method from class EntityViewGenerator to generate the views, while the Entity Framework 6 test used the GenerateViews() method from class StorageMappingItemCollection.</span></span> <span data-ttu-id="820d5-296">這是因為在 Entity Framework 6 程式碼基底中發生的程式碼重建。</span><span class="sxs-lookup"><span data-stu-id="820d5-296">This due to code restructuring that occurred in the Entity Framework 6 codebase.</span></span>

<span data-ttu-id="820d5-297">使用 Entity Framework 5，在實驗室機器中，具有外鍵的模型產生會花費65分鐘的時間。</span><span class="sxs-lookup"><span data-stu-id="820d5-297">Using Entity Framework 5, view generation for the model with Foreign Keys took 65 minutes in a lab machine.</span></span> <span data-ttu-id="820d5-298">為使用獨立關聯的模型產生視圖所需的時間是未知的。</span><span class="sxs-lookup"><span data-stu-id="820d5-298">It's unknown how long it would have taken to generate the views for the model that used independent associations.</span></span> <span data-ttu-id="820d5-299">在我們的實驗室中，我們已將執行的測試保留一個月以上，以安裝每月更新。</span><span class="sxs-lookup"><span data-stu-id="820d5-299">We left the test running for over a month before the machine was rebooted in our lab to install monthly updates.</span></span>

<span data-ttu-id="820d5-300">使用 Entity Framework 6，在相同的實驗室電腦中，具有外鍵的模型產生會花費28秒。</span><span class="sxs-lookup"><span data-stu-id="820d5-300">Using Entity Framework 6, view generation for the model with Foreign Keys took 28 seconds in the same lab machine.</span></span> <span data-ttu-id="820d5-301">使用獨立關聯的模型產生視圖會耗用58秒。</span><span class="sxs-lookup"><span data-stu-id="820d5-301">View generation for the model that uses Independent Associations took 58 seconds.</span></span> <span data-ttu-id="820d5-302">在其視圖產生程式碼上對 Entity Framework 6 所做的改善，表示許多專案不需要預先產生的視圖，即可取得更快速的啟動時間。</span><span class="sxs-lookup"><span data-stu-id="820d5-302">The improvements done to Entity Framework 6 on its view generation code mean that many projects won’t need pre-generated views to obtain faster startup times.</span></span>

<span data-ttu-id="820d5-303">請務必注意，Entity Framework 4 和5中預先產生的視圖可以透過 Edmgen.exe 或 Entity Framework Power tool 來完成。</span><span class="sxs-lookup"><span data-stu-id="820d5-303">It’s important to remark that pre-generating views in Entity Framework 4 and 5 can be done with EDMGen or the Entity Framework Power Tools.</span></span> <span data-ttu-id="820d5-304">針對 Entity Framework 6 視圖產生可以透過 Entity Framework 的 Power tool 或程式設計方式來完成，如[預先產生的對應視圖](~/ef6/fundamentals/performance/pre-generated-views.md)中所述。</span><span class="sxs-lookup"><span data-stu-id="820d5-304">For Entity Framework 6 view generation can be done via the Entity Framework Power Tools or programmatically as described in [Pre-Generated Mapping Views](~/ef6/fundamentals/performance/pre-generated-views.md).</span></span>

##### <a name="2411-how-to-use-foreign-keys-instead-of-independent-associations"></a><span data-ttu-id="820d5-305">2.4.1.1 如何使用外鍵，而不是獨立關聯</span><span class="sxs-lookup"><span data-stu-id="820d5-305">2.4.1.1 How to use Foreign Keys instead of Independent Associations</span></span>

<span data-ttu-id="820d5-306">使用 Edmgen.exe 或 Visual Studio 中的 Entity Designer 時，您預設會取得 Fk，而且只會使用單一核取方塊或命令列旗標，在 Fk 與 IAs 之間切換。</span><span class="sxs-lookup"><span data-stu-id="820d5-306">When using EDMGen or the Entity Designer in Visual Studio, you get FKs by default, and it only takes a single checkbox or command line flag to switch between FKs and IAs.</span></span>

<span data-ttu-id="820d5-307">如果您有大型的 Code First 模型，使用獨立關聯的效果會與產生視圖相同。</span><span class="sxs-lookup"><span data-stu-id="820d5-307">If you have a large Code First model, using Independent Associations will have the same effect on view generation.</span></span> <span data-ttu-id="820d5-308">您可以在相依物件的類別上包含外鍵屬性來避免這種影響，不過有些開發人員會將其視為物件模型的污染。</span><span class="sxs-lookup"><span data-stu-id="820d5-308">You can avoid this impact by including Foreign Key properties on the classes for your dependent objects, though some developers will consider this to be polluting their object model.</span></span> <span data-ttu-id="820d5-309">您可以在 \<http://blog.oneunicorn.com/2011/12/11/whats-the-deal-with-mapping-foreign-keys-using-the-entity-framework/>中找到此主題的詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="820d5-309">You can find more information on this subject in \<http://blog.oneunicorn.com/2011/12/11/whats-the-deal-with-mapping-foreign-keys-using-the-entity-framework/>.</span></span>

| <span data-ttu-id="820d5-310">使用時</span><span class="sxs-lookup"><span data-stu-id="820d5-310">When using</span></span>      | <span data-ttu-id="820d5-311">請這樣做</span><span class="sxs-lookup"><span data-stu-id="820d5-311">Do this</span></span>                                                                                                                                                                                                                                                                                                                              |
|:----------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="820d5-312">Entity Designer</span><span class="sxs-lookup"><span data-stu-id="820d5-312">Entity Designer</span></span> | <span data-ttu-id="820d5-313">加入兩個實體之間的關聯之後，請確定您有參考條件約束。</span><span class="sxs-lookup"><span data-stu-id="820d5-313">After adding an association between two entities, make sure you have a referential constraint.</span></span> <span data-ttu-id="820d5-314">參考條件約束會告訴 Entity Framework 使用外鍵，而不是獨立的關聯。</span><span class="sxs-lookup"><span data-stu-id="820d5-314">Referential constraints tell Entity Framework to use Foreign Keys instead of Independent Associations.</span></span> <span data-ttu-id="820d5-315">如需其他詳細資料，請造訪 \<http://blogs.msdn.com/b/efdesign/archive/2009/03/16/foreign-keys-in-the-entity-framework.aspx>。</span><span class="sxs-lookup"><span data-stu-id="820d5-315">For additional details visit \<http://blogs.msdn.com/b/efdesign/archive/2009/03/16/foreign-keys-in-the-entity-framework.aspx>.</span></span> |
| <span data-ttu-id="820d5-316">Edmgen.exe</span><span class="sxs-lookup"><span data-stu-id="820d5-316">EDMGen</span></span>          | <span data-ttu-id="820d5-317">使用 Edmgen.exe 從資料庫產生您的檔案時，將會遵守您的外鍵，並將其加入至模型中。</span><span class="sxs-lookup"><span data-stu-id="820d5-317">When using EDMGen to generate your files from the database, your Foreign Keys will be respected and added to the model as such.</span></span> <span data-ttu-id="820d5-318">如需 Edmgen.exe 所公開之不同選項的詳細資訊，請造訪[http://msdn.microsoft.com/library/bb387165.aspx](https://msdn.microsoft.com/library/bb387165.aspx)。</span><span class="sxs-lookup"><span data-stu-id="820d5-318">For more information on the different options exposed by EDMGen visit [http://msdn.microsoft.com/library/bb387165.aspx](https://msdn.microsoft.com/library/bb387165.aspx).</span></span>                           |
| <span data-ttu-id="820d5-319">Code First</span><span class="sxs-lookup"><span data-stu-id="820d5-319">Code First</span></span>      | <span data-ttu-id="820d5-320">如需有關使用 Code First 時如何在相依物件上包含外鍵屬性的資訊，請參閱[Code First 慣例](~/ef6/modeling/code-first/conventions/built-in.md)主題的「關聯性慣例」一節。</span><span class="sxs-lookup"><span data-stu-id="820d5-320">See the "Relationship Convention" section of the [Code First Conventions](~/ef6/modeling/code-first/conventions/built-in.md) topic for information on how to include foreign key properties on dependent objects when using Code First.</span></span>                                                                                              |

#### <a name="242-moving-your-model-to-a-separate-assembly"></a><span data-ttu-id="820d5-321">2.4.2 將模型移至不同的元件</span><span class="sxs-lookup"><span data-stu-id="820d5-321">2.4.2 Moving your model to a separate assembly</span></span>

<span data-ttu-id="820d5-322">當您的模型直接包含在應用程式的專案中，而您透過預先建立事件或 T4 範本產生 views 時，只要重建專案，就會發生視圖產生和驗證，即使模型並未變更也是一樣。</span><span class="sxs-lookup"><span data-stu-id="820d5-322">When your model is included directly in your application's project and you generate views through a pre-build event or a T4 template, view generation and validation will take place whenever the project is rebuilt, even if the model wasn't changed.</span></span> <span data-ttu-id="820d5-323">如果您將模型移至不同的元件，並從應用程式的專案中參考它，您可以對應用程式進行其他變更，而不需要重建包含模型的專案。</span><span class="sxs-lookup"><span data-stu-id="820d5-323">If you move the model to a separate assembly and reference it from your application's project, you can make other changes to your application without needing to rebuild the project containing the model.</span></span>

<span data-ttu-id="820d5-324">*注意：* 將模型移至不同的元件時  ，請記得將模型的連接字串複製到用戶端專案的應用程式佈建檔中。</span><span class="sxs-lookup"><span data-stu-id="820d5-324">*Note:*  when moving your model to separate assemblies remember to copy the connection strings for the model into the application configuration file of the client project.</span></span>

#### <a name="243-disable-validation-of-an-edmx-based-model"></a><span data-ttu-id="820d5-325">2.4.3 停用以 edmx 為基礎的模型驗證</span><span class="sxs-lookup"><span data-stu-id="820d5-325">2.4.3 Disable validation of an edmx-based model</span></span>

<span data-ttu-id="820d5-326">EDMX 模型會在編譯時期進行驗證，即使模型不變也一樣。</span><span class="sxs-lookup"><span data-stu-id="820d5-326">EDMX models are validated at compile time, even if the model is unchanged.</span></span> <span data-ttu-id="820d5-327">如果您的模型已經過驗證，您可以在編譯時期隱藏驗證，方法是在 [屬性] 視窗中將 [在組建上驗證] 屬性設定為 [false]。</span><span class="sxs-lookup"><span data-stu-id="820d5-327">If your model has already been validated, you can suppress validation at compile time by setting the "Validate on Build" property to false in the properties window.</span></span> <span data-ttu-id="820d5-328">當您變更對應或模型時，可以暫時重新啟用驗證來驗證您的變更。</span><span class="sxs-lookup"><span data-stu-id="820d5-328">When you change your mapping or model, you can temporarily re-enable validation to verify your changes.</span></span>

<span data-ttu-id="820d5-329">請注意，已改善 Entity Framework 6 的 Entity Framework Designer，而「在組建上進行驗證」的成本遠低於舊版的設計工具。</span><span class="sxs-lookup"><span data-stu-id="820d5-329">Note that performance improvements were made to the Entity Framework Designer for Entity Framework 6, and the cost of the “Validate on Build” is much lower than in previous versions of the designer.</span></span>

## <a name="3-caching-in-the-entity-framework"></a><span data-ttu-id="820d5-330">3 Entity Framework 中的快取</span><span class="sxs-lookup"><span data-stu-id="820d5-330">3 Caching in the Entity Framework</span></span>

<span data-ttu-id="820d5-331">Entity Framework 具有下列內建的快取形式：</span><span class="sxs-lookup"><span data-stu-id="820d5-331">Entity Framework has the following forms of caching built-in:</span></span>

1.  <span data-ttu-id="820d5-332">物件快取–內建于 ObjectCoNtext 實例中的 ObjectStateManager 會追蹤使用該實例所抓取之物件的記憶體。</span><span class="sxs-lookup"><span data-stu-id="820d5-332">Object caching – the ObjectStateManager built into an ObjectContext instance keeps track in memory of the objects that have been retrieved using that instance.</span></span> <span data-ttu-id="820d5-333">這也稱為「第一層快取」。</span><span class="sxs-lookup"><span data-stu-id="820d5-333">This is also known as first-level cache.</span></span>
2.  <span data-ttu-id="820d5-334">查詢計劃快取-執行多次查詢時，重複使用所產生的存放區命令。</span><span class="sxs-lookup"><span data-stu-id="820d5-334">Query Plan Caching - reusing the generated store command when a query is executed more than once.</span></span>
3.  <span data-ttu-id="820d5-335">中繼資料快取-在相同模型的不同連接之間共用模型的中繼資料。</span><span class="sxs-lookup"><span data-stu-id="820d5-335">Metadata caching - sharing the metadata for a model across different connections to the same model.</span></span>

<span data-ttu-id="820d5-336">除了 EF 提供的快取之外，一種特殊的 ADO.NET 資料提供者（稱為包裝提供者）也可以用來針對從資料庫中取得的結果（也稱為第二層快取）來擴充 Entity Framework。</span><span class="sxs-lookup"><span data-stu-id="820d5-336">Besides the caches that EF provides out of the box, a special kind of ADO.NET data provider known as a wrapping provider can also be used to extend Entity Framework with a cache for the results retrieved from the database, also known as second-level caching.</span></span>

### <a name="31-object-caching"></a><span data-ttu-id="820d5-337">3.1 物件快取</span><span class="sxs-lookup"><span data-stu-id="820d5-337">3.1 Object Caching</span></span>

<span data-ttu-id="820d5-338">根據預設，在查詢結果中傳回實體時，在 EF 具體化之前，ObjectCoNtext 會檢查是否已將具有相同索引鍵的實體載入其 ObjectStateManager 中。</span><span class="sxs-lookup"><span data-stu-id="820d5-338">By default when an entity is returned in the results of a query, just before EF materializes it, the ObjectContext will check if an entity with the same key has already been loaded into its ObjectStateManager.</span></span> <span data-ttu-id="820d5-339">如果已有具有相同索引鍵的實體存在，EF 會將它包含在查詢的結果中。</span><span class="sxs-lookup"><span data-stu-id="820d5-339">If an entity with the same keys is already present EF will include it in the results of the query.</span></span> <span data-ttu-id="820d5-340">雖然 EF 仍然會針對資料庫發出查詢，但此行為可能會略過多個具體化實體的大部分成本。</span><span class="sxs-lookup"><span data-stu-id="820d5-340">Although EF will still issue the query against the database, this behavior can bypass much of the cost of materializing the entity multiple times.</span></span>

#### <a name="311-getting-entities-from-the-object-cache-using-dbcontext-find"></a><span data-ttu-id="820d5-341">3.1.1 使用 DbCoNtext 尋找從物件快取取得實體</span><span class="sxs-lookup"><span data-stu-id="820d5-341">3.1.1 Getting entities from the object cache using DbContext Find</span></span>

<span data-ttu-id="820d5-342">不同于一般查詢，DbSet （EF 4.1 中第一次包含的 Api）中的 Find 方法將會在記憶體中執行搜尋，甚至是針對資料庫發出查詢。</span><span class="sxs-lookup"><span data-stu-id="820d5-342">Unlike a regular query, the Find method in DbSet (APIs included for the first time in EF 4.1) will perform a search in memory before even issuing the query against the database.</span></span> <span data-ttu-id="820d5-343">請務必注意，兩個不同的 ObjectCoNtext 實例會有兩個不同的 ObjectStateManager 實例，這表示它們有不同的物件快取。</span><span class="sxs-lookup"><span data-stu-id="820d5-343">It’s important to note that two different ObjectContext instances will have two different ObjectStateManager instances, meaning that they have separate object caches.</span></span>

<span data-ttu-id="820d5-344">尋找會使用主要金鑰值來嘗試尋找內容所追蹤的實體。</span><span class="sxs-lookup"><span data-stu-id="820d5-344">Find uses the primary key value to attempt to find an entity tracked by the context.</span></span> <span data-ttu-id="820d5-345">如果實體不在內容中，則會針對資料庫執行並評估查詢，如果在內容或資料庫中找不到實體，則會傳回 null。</span><span class="sxs-lookup"><span data-stu-id="820d5-345">If the entity is not in the context then a query will be executed and evaluated against the database, and null is returned if the entity is not found in the context or in the database.</span></span> <span data-ttu-id="820d5-346">請注意，Find 也會傳回已加入至內容，但尚未儲存到資料庫的實體。</span><span class="sxs-lookup"><span data-stu-id="820d5-346">Note that Find also returns entities that have been added to the context but have not yet been saved to the database.</span></span>

<span data-ttu-id="820d5-347">使用 [尋找] 時，應採取效能考慮。</span><span class="sxs-lookup"><span data-stu-id="820d5-347">There is a performance consideration to be taken when using Find.</span></span> <span data-ttu-id="820d5-348">根據預設，叫用此方法將會觸發物件快取的驗證，以偵測仍在等待資料庫認可的變更。</span><span class="sxs-lookup"><span data-stu-id="820d5-348">Invocations to this method by default will trigger a validation of the object cache in order to detect changes that are still pending commit to the database.</span></span> <span data-ttu-id="820d5-349">如果物件快取中有非常大量的物件，或在大型物件圖形中新增至物件快取，則此程式可能非常昂貴，但也可以停用。</span><span class="sxs-lookup"><span data-stu-id="820d5-349">This process can be very expensive if there are a very large number of objects in the object cache or in a large object graph being added to the object cache, but it can also be disabled.</span></span> <span data-ttu-id="820d5-350">在某些情況下，當您停用自動偵測變更時，您可能會察覺到呼叫 Find 方法的差異程度。</span><span class="sxs-lookup"><span data-stu-id="820d5-350">In certain cases, you may perceive over an order of magnitude of difference in calling the Find method when you disable auto detect changes.</span></span> <span data-ttu-id="820d5-351">但是當物件實際上是在快取中，而物件必須從資料庫中抓取時，會發現第二個數量級的順序。</span><span class="sxs-lookup"><span data-stu-id="820d5-351">Yet a second order of magnitude is perceived when the object actually is in the cache versus when the object has to be retrieved from the database.</span></span> <span data-ttu-id="820d5-352">以下是使用我們的一些 microbenchmarks （以毫秒為單位）來測量的範例圖形，其中包含5000個實體的負載：</span><span class="sxs-lookup"><span data-stu-id="820d5-352">Here is an example graph with measurements taken using some of our microbenchmarks, expressed in milliseconds, with a load of 5000 entities:</span></span>

<span data-ttu-id="820d5-353">![.Net 4.5 對數刻度](~/ef6/media/net45logscale.png ".net 4.5-對數刻度")</span><span class="sxs-lookup"><span data-stu-id="820d5-353">![.NET 4.5 logarithmic scale](~/ef6/media/net45logscale.png ".NET 4.5 - logarithmic scale")</span></span>

<span data-ttu-id="820d5-354">已停用自動偵測變更的尋找範例：</span><span class="sxs-lookup"><span data-stu-id="820d5-354">Example of Find with auto-detect changes disabled:</span></span>

``` csharp
    context.Configuration.AutoDetectChangesEnabled = false;
    var product = context.Products.Find(productId);
    context.Configuration.AutoDetectChangesEnabled = true;
    ...
```

<span data-ttu-id="820d5-355">使用 Find 方法時必須考慮的事項是：</span><span class="sxs-lookup"><span data-stu-id="820d5-355">What you have to consider when using the Find method is:</span></span>

1.  <span data-ttu-id="820d5-356">如果物件不在快取中，則尋找的優點會是否定的，但語法仍然比依索引鍵的查詢更簡單。</span><span class="sxs-lookup"><span data-stu-id="820d5-356">If the object is not in the cache the benefits of Find are negated, but the syntax is still simpler than a query by key.</span></span>
2.  <span data-ttu-id="820d5-357">如果已啟用自動偵測變更，尋找方法的成本可能會增加一個程度，或甚至更多，視您的模型複雜度和物件快取中的實體量而定。</span><span class="sxs-lookup"><span data-stu-id="820d5-357">If auto detect changes is enabled the cost of the Find method may increase by one order of magnitude, or even more depending on the complexity of your model and the amount of entities in your object cache.</span></span>

<span data-ttu-id="820d5-358">此外，請記住，尋找只會傳回您要尋找的實體，如果物件快取中還沒有關聯的實體，它就不會自動載入它們。</span><span class="sxs-lookup"><span data-stu-id="820d5-358">Also, keep in mind that Find only returns the entity you are looking for and it does not automatically loads its associated entities if they are not already in the object cache.</span></span> <span data-ttu-id="820d5-359">如果您需要抓取相關聯的實體，您可以使用依索引鍵進行的查詢與積極式載入。</span><span class="sxs-lookup"><span data-stu-id="820d5-359">If you need to retrieve associated entities, you can use a query by key with eager loading.</span></span> <span data-ttu-id="820d5-360">如需詳細資訊，請參閱8.1 消極式**載入和積極式載入**。</span><span class="sxs-lookup"><span data-stu-id="820d5-360">For more information see **8.1 Lazy Loading vs. Eager Loading**.</span></span>

#### <a name="312-performance-issues-when-the-object-cache-has-many-entities"></a><span data-ttu-id="820d5-361">當物件快取有許多實體時，3.1.2 效能問題</span><span class="sxs-lookup"><span data-stu-id="820d5-361">3.1.2 Performance issues when the object cache has many entities</span></span>

<span data-ttu-id="820d5-362">物件快取有助於增加 Entity Framework 的整體回應性。</span><span class="sxs-lookup"><span data-stu-id="820d5-362">The object cache helps to increase the overall responsiveness of Entity Framework.</span></span> <span data-ttu-id="820d5-363">不過，當物件快取已載入非常大量的實體時，可能會影響某些作業，例如新增、移除、尋找、輸入、SaveChanges 等等。</span><span class="sxs-lookup"><span data-stu-id="820d5-363">However, when the object cache has a very large amount of entities loaded it may affect certain operations such as Add, Remove, Find, Entry, SaveChanges and more.</span></span> <span data-ttu-id="820d5-364">特別是，觸發呼叫 DetectChanges 的作業會受到非常大型物件快取的負面影響。</span><span class="sxs-lookup"><span data-stu-id="820d5-364">In particular, operations that trigger a call to DetectChanges will be negatively affected by very large object caches.</span></span> <span data-ttu-id="820d5-365">DetectChanges 會同步處理物件圖形與物件狀態管理員，而且其效能會直接由物件圖形的大小決定。</span><span class="sxs-lookup"><span data-stu-id="820d5-365">DetectChanges synchronizes the object graph with the object state manager and its performance will determined directly by the size of the object graph.</span></span> <span data-ttu-id="820d5-366">如需 DetectChanges 的詳細資訊，請參閱[追蹤 POCO 實體中的變更](https://msdn.microsoft.com/library/dd456848.aspx)。</span><span class="sxs-lookup"><span data-stu-id="820d5-366">For more information about DetectChanges, see [Tracking Changes in POCO Entities](https://msdn.microsoft.com/library/dd456848.aspx).</span></span>

<span data-ttu-id="820d5-367">使用 Entity Framework 6 時，開發人員可以直接在 DbSet 上呼叫 AddRange 和 RemoveRange，而不是逐一查看集合，並針對每個實例呼叫 Add 一次。</span><span class="sxs-lookup"><span data-stu-id="820d5-367">When using Entity Framework 6, developers are able to call AddRange and RemoveRange directly on a DbSet, instead of iterating on a collection and calling Add once per instance.</span></span> <span data-ttu-id="820d5-368">使用範圍方法的優點是，DetectChanges 的成本只會針對整個實體集付費一次，而不會針對每個新增的實體支付一次。</span><span class="sxs-lookup"><span data-stu-id="820d5-368">The advantage of using the range methods is that the cost of DetectChanges is only paid once for the entire set of entities as opposed to once per each added entity.</span></span>

### <a name="32-query-plan-caching"></a><span data-ttu-id="820d5-369">3.2 查詢計劃快取</span><span class="sxs-lookup"><span data-stu-id="820d5-369">3.2 Query Plan Caching</span></span>

<span data-ttu-id="820d5-370">第一次執行查詢時，它會通過內部計畫編譯器，將概念查詢轉譯為 store 命令（例如，針對 SQL Server 執行時所執行的 T-sql）。</span><span class="sxs-lookup"><span data-stu-id="820d5-370">The first time a query is executed, it goes through the internal plan compiler to translate the conceptual query into the store command (for example, the T-SQL which is executed when run against SQL Server).</span></span><span data-ttu-id="820d5-371">  如果已啟用查詢計劃快取，則下一次執行查詢時，會直接從查詢計劃快取中取得 store 命令以進行執行，略過計畫編譯器。</span><span class="sxs-lookup"><span data-stu-id="820d5-371">  If query plan caching is enabled, the next time the query is executed the store command is retrieved directly from the query plan cache for execution, bypassing the plan compiler.</span></span>

<span data-ttu-id="820d5-372">查詢計劃快取會在相同 AppDomain 內的 ObjectCoNtext 實例之間共用。</span><span class="sxs-lookup"><span data-stu-id="820d5-372">The query plan cache is shared across ObjectContext instances within the same AppDomain.</span></span> <span data-ttu-id="820d5-373">您不需要保留 ObjectCoNtext 實例，就能受益于查詢計劃快取。</span><span class="sxs-lookup"><span data-stu-id="820d5-373">You don't need to hold onto an ObjectContext instance to benefit from query plan caching.</span></span>

#### <a name="321-some-notes-about-query-plan-caching"></a><span data-ttu-id="820d5-374">3.2.1 關於查詢計劃快取的一些注意事項</span><span class="sxs-lookup"><span data-stu-id="820d5-374">3.2.1 Some notes about Query Plan Caching</span></span>

-   <span data-ttu-id="820d5-375">查詢計劃快取會針對所有查詢類型共用： Entity SQL、LINQ to Entities 和 CompiledQuery 物件。</span><span class="sxs-lookup"><span data-stu-id="820d5-375">The query plan cache is shared for all query types: Entity SQL, LINQ to Entities, and CompiledQuery objects.</span></span>
-   <span data-ttu-id="820d5-376">根據預設，查詢計劃快取會針對 Entity SQL 查詢而啟用，不論是透過 EntityCommand 或透過 ObjectQuery 來執行。</span><span class="sxs-lookup"><span data-stu-id="820d5-376">By default, query plan caching is enabled for Entity SQL queries, whether executed through an EntityCommand or through an ObjectQuery.</span></span> <span data-ttu-id="820d5-377">在 .NET 4.5 上的 Entity Framework 中 LINQ to Entities 查詢，以及 Entity Framework 6，預設也會啟用此功能。</span><span class="sxs-lookup"><span data-stu-id="820d5-377">It is also enabled by default for LINQ to Entities queries in Entity Framework on .NET 4.5, and in Entity Framework 6</span></span>
    -   <span data-ttu-id="820d5-378">將 EnablePlanCaching 屬性（在 EntityCommand 或 ObjectQuery 上）設定為 false，即可停用查詢計劃快取。</span><span class="sxs-lookup"><span data-stu-id="820d5-378">Query plan caching can be disabled by setting the EnablePlanCaching property (on EntityCommand or ObjectQuery) to false.</span></span> <span data-ttu-id="820d5-379">例如：</span><span class="sxs-lookup"><span data-stu-id="820d5-379">For example:</span></span>
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
-   <span data-ttu-id="820d5-380">對於參數化查詢，變更參數的值仍會到達快取的查詢。</span><span class="sxs-lookup"><span data-stu-id="820d5-380">For parameterized queries, changing the parameter's value will still hit the cached query.</span></span> <span data-ttu-id="820d5-381">但變更參數的 facet （例如，大小、有效位數或小數位數）會在快取中達到不同的專案。</span><span class="sxs-lookup"><span data-stu-id="820d5-381">But changing a parameter's facets (for example, size, precision, or scale) will hit a different entry in the cache.</span></span>
-   <span data-ttu-id="820d5-382">使用 Entity SQL 時，查詢字串是索引鍵的一部分。</span><span class="sxs-lookup"><span data-stu-id="820d5-382">When using Entity SQL, the query string is part of the key.</span></span> <span data-ttu-id="820d5-383">同時變更查詢會產生不同的快取專案，即使查詢的功能相同也一樣。</span><span class="sxs-lookup"><span data-stu-id="820d5-383">Changing the query at all will result in different cache entries, even if the queries are functionally equivalent.</span></span> <span data-ttu-id="820d5-384">這包括大小寫或空白字元的變更。</span><span class="sxs-lookup"><span data-stu-id="820d5-384">This includes changes to casing or whitespace.</span></span>
-   <span data-ttu-id="820d5-385">使用 LINQ 時，會處理查詢以產生部分的索引鍵。</span><span class="sxs-lookup"><span data-stu-id="820d5-385">When using LINQ, the query is processed to generate a part of the key.</span></span> <span data-ttu-id="820d5-386">因此，變更 LINQ 運算式會產生不同的索引鍵。</span><span class="sxs-lookup"><span data-stu-id="820d5-386">Changing the LINQ expression will therefore generate a different key.</span></span>
-   <span data-ttu-id="820d5-387">可能適用其他技術限制;如需詳細資訊，請參閱 Autocompiled 查詢。</span><span class="sxs-lookup"><span data-stu-id="820d5-387">Other technical limitations may apply; see Autocompiled Queries for more details.</span></span>

#### <a name="322-cache-eviction-algorithm"></a><span data-ttu-id="820d5-388">3.2.2 快取收回演算法</span><span class="sxs-lookup"><span data-stu-id="820d5-388">3.2.2      Cache eviction algorithm</span></span>

<span data-ttu-id="820d5-389">瞭解內部演算法的運作方式，可協助您找出啟用或停用查詢計劃快取的時機。</span><span class="sxs-lookup"><span data-stu-id="820d5-389">Understanding how the internal algorithm works will help you figure out when to enable or disable query plan caching.</span></span> <span data-ttu-id="820d5-390">清除演算法如下所示：</span><span class="sxs-lookup"><span data-stu-id="820d5-390">The cleanup algorithm is as follows:</span></span>

1.  <span data-ttu-id="820d5-391">一旦快取包含設定的專案數（800），我們就會開始定期（每分鐘一次）掃描快取的計時器。</span><span class="sxs-lookup"><span data-stu-id="820d5-391">Once the cache contains a set number of entries (800), we start a timer that periodically (once-per-minute) sweeps the cache.</span></span>
2.  <span data-ttu-id="820d5-392">在快取掃描期間，會以 LFRU （最不常使用）為基礎，從快取中移除專案。</span><span class="sxs-lookup"><span data-stu-id="820d5-392">During cache sweeps, entries are removed from the cache on a LFRU (Least frequently – recently used) basis.</span></span> <span data-ttu-id="820d5-393">在決定要彈出哪些專案時，此演算法會將 [計數] 和 [年齡] 納入考慮。</span><span class="sxs-lookup"><span data-stu-id="820d5-393">This algorithm takes both hit count and age into account when deciding which entries are ejected.</span></span>
3.  <span data-ttu-id="820d5-394">在每個快取清除結束時，快取再次包含800個專案。</span><span class="sxs-lookup"><span data-stu-id="820d5-394">At the end of each cache sweep, the cache again contains 800 entries.</span></span>

<span data-ttu-id="820d5-395">在決定要收回的專案時，會同等地處理所有快取專案。</span><span class="sxs-lookup"><span data-stu-id="820d5-395">All cache entries are treated equally when determining which entries to evict.</span></span> <span data-ttu-id="820d5-396">這表示 CompiledQuery 的存放區命令與 Entity SQL 查詢的存放區命令具有相同的收回機會。</span><span class="sxs-lookup"><span data-stu-id="820d5-396">This means the store command for a CompiledQuery has the same chance of eviction as the store command for an Entity SQL query.</span></span>

<span data-ttu-id="820d5-397">請注意，當快取中有800個實體時，快取收回計時器會在中啟動，但快取只會在此計時器開始之後掃描60秒。</span><span class="sxs-lookup"><span data-stu-id="820d5-397">Note that the cache eviction timer is kicked in when there are 800 entities in the cache, but the cache is only swept 60 seconds after this timer is started.</span></span> <span data-ttu-id="820d5-398">這表示最多60秒，您的快取可能會變得相當大。</span><span class="sxs-lookup"><span data-stu-id="820d5-398">That means that for up to 60 seconds your cache may grow to be quite large.</span></span>

#### <a name="323-test-metrics-demonstrating-query-plan-caching-performance"></a><span data-ttu-id="820d5-399">示範查詢計劃快取效能的3.2.3 測試度量</span><span class="sxs-lookup"><span data-stu-id="820d5-399">3.2.3       Test Metrics demonstrating query plan caching performance</span></span>

<span data-ttu-id="820d5-400">為了示範查詢計劃快取對應用程式效能的影響，我們執行了一項測試，讓我們針對 Navision 模型執行數個 Entity SQL 查詢。</span><span class="sxs-lookup"><span data-stu-id="820d5-400">To demonstrate the effect of query plan caching on your application's performance, we performed a test where we executed a number of Entity SQL queries against the Navision model.</span></span> <span data-ttu-id="820d5-401">如需 Navision 模型的描述，以及已執行之查詢的類型，請參閱附錄。</span><span class="sxs-lookup"><span data-stu-id="820d5-401">See the appendix for a description of the Navision model and the types of queries which were executed.</span></span> <span data-ttu-id="820d5-402">在這項測試中，我們會先逐一查看查詢清單，並執行每一次，將它們新增至快取（如果已啟用快取）。</span><span class="sxs-lookup"><span data-stu-id="820d5-402">In this test, we first iterate through the list of queries and execute each one once to add them to the cache (if caching is enabled).</span></span> <span data-ttu-id="820d5-403">此步驟為 untimed。</span><span class="sxs-lookup"><span data-stu-id="820d5-403">This step is untimed.</span></span> <span data-ttu-id="820d5-404">接下來，我們會睡眠超過60秒的主執行緒，以允許進行快取的掃描;最後，我們會逐一查看清單第2次，以執行快取的查詢。</span><span class="sxs-lookup"><span data-stu-id="820d5-404">Next, we sleep the main thread for over 60 seconds to allow cache sweeping to take place; finally, we iterate through the list a 2nd time to execute the cached queries.</span></span> <span data-ttu-id="820d5-405">此外，在執行每一組查詢之前，會先清除 SQL Server 計畫快取，讓我們準確地反映查詢計劃快取所提供的好處。</span><span class="sxs-lookup"><span data-stu-id="820d5-405">Additionally, the SQL Server plan cache is flushed before each set of queries is executed so that the times we obtain accurately reflect the benefit given by the query plan cache.</span></span>

##### <a name="3231-test-results"></a><span data-ttu-id="820d5-406">3.2.3.1 測試結果</span><span class="sxs-lookup"><span data-stu-id="820d5-406">3.2.3.1       Test Results</span></span>

| <span data-ttu-id="820d5-407">測試</span><span class="sxs-lookup"><span data-stu-id="820d5-407">Test</span></span>                                                                   | <span data-ttu-id="820d5-408">EF5 無快取</span><span class="sxs-lookup"><span data-stu-id="820d5-408">EF5 no cache</span></span> | <span data-ttu-id="820d5-409">EF5 快取</span><span class="sxs-lookup"><span data-stu-id="820d5-409">EF5 cached</span></span> | <span data-ttu-id="820d5-410">EF6 無快取</span><span class="sxs-lookup"><span data-stu-id="820d5-410">EF6 no cache</span></span> | <span data-ttu-id="820d5-411">EF6 快取</span><span class="sxs-lookup"><span data-stu-id="820d5-411">EF6 cached</span></span> |
|:-----------------------------------------------------------------------|:-------------|:-----------|:-------------|:-----------|
| <span data-ttu-id="820d5-412">列舉所有18723查詢</span><span class="sxs-lookup"><span data-stu-id="820d5-412">Enumerating all 18723 queries</span></span>                                          | <span data-ttu-id="820d5-413">124</span><span class="sxs-lookup"><span data-stu-id="820d5-413">124</span></span>          | <span data-ttu-id="820d5-414">125.4</span><span class="sxs-lookup"><span data-stu-id="820d5-414">125.4</span></span>      | <span data-ttu-id="820d5-415">124.3</span><span class="sxs-lookup"><span data-stu-id="820d5-415">124.3</span></span>        | <span data-ttu-id="820d5-416">125.3</span><span class="sxs-lookup"><span data-stu-id="820d5-416">125.3</span></span>      |
| <span data-ttu-id="820d5-417">避免清理（僅限前800個查詢，不論複雜度為何）</span><span class="sxs-lookup"><span data-stu-id="820d5-417">Avoiding sweep (just the first 800 queries, regardless of complexity)</span></span>  | <span data-ttu-id="820d5-418">41.7</span><span class="sxs-lookup"><span data-stu-id="820d5-418">41.7</span></span>         | <span data-ttu-id="820d5-419">5.5</span><span class="sxs-lookup"><span data-stu-id="820d5-419">5.5</span></span>        | <span data-ttu-id="820d5-420">40.5</span><span class="sxs-lookup"><span data-stu-id="820d5-420">40.5</span></span>         | <span data-ttu-id="820d5-421">5.4</span><span class="sxs-lookup"><span data-stu-id="820d5-421">5.4</span></span>        |
| <span data-ttu-id="820d5-422">只有 AggregatingSubtotals 查詢（總共178個-這可避免清除）</span><span class="sxs-lookup"><span data-stu-id="820d5-422">Just the AggregatingSubtotals queries (178 total - which avoids sweep)</span></span> | <span data-ttu-id="820d5-423">39.5</span><span class="sxs-lookup"><span data-stu-id="820d5-423">39.5</span></span>         | <span data-ttu-id="820d5-424">4.5</span><span class="sxs-lookup"><span data-stu-id="820d5-424">4.5</span></span>        | <span data-ttu-id="820d5-425">38.1</span><span class="sxs-lookup"><span data-stu-id="820d5-425">38.1</span></span>         | <span data-ttu-id="820d5-426">4.6</span><span class="sxs-lookup"><span data-stu-id="820d5-426">4.6</span></span>        |

<span data-ttu-id="820d5-427">*所有時間（以秒為單位）。*</span><span class="sxs-lookup"><span data-stu-id="820d5-427">*All times in seconds.*</span></span>

<span data-ttu-id="820d5-428">道德-執行許多不同的查詢（例如，動態建立的查詢）時，快取不會有説明，而所產生的快取清除，可以讓從計畫快取中獲益最多的查詢，從實際使用它。</span><span class="sxs-lookup"><span data-stu-id="820d5-428">Moral - when executing lots of distinct queries (for example,  dynamically created queries), caching doesn't help and the resulting flushing of the cache can keep the queries that would benefit the most from plan caching from actually using it.</span></span>

<span data-ttu-id="820d5-429">AggregatingSubtotals 查詢是我們用來測試的最複雜查詢。</span><span class="sxs-lookup"><span data-stu-id="820d5-429">The AggregatingSubtotals queries are the most complex of the queries we tested with.</span></span> <span data-ttu-id="820d5-430">如預期，查詢越複雜，您就可以從查詢計劃快取中看到更多的好處。</span><span class="sxs-lookup"><span data-stu-id="820d5-430">As expected, the more complex the query is, the more benefit you will see from query plan caching.</span></span>

<span data-ttu-id="820d5-431">由於 CompiledQuery 其實是已快取其計畫的 LINQ 查詢，因此 CompiledQuery 與對等 Entity SQL 查詢的比較應該會有類似的結果。</span><span class="sxs-lookup"><span data-stu-id="820d5-431">Because a CompiledQuery is really a LINQ query with its plan cached, the comparison of a CompiledQuery versus the equivalent Entity SQL query should have similar results.</span></span> <span data-ttu-id="820d5-432">事實上，如果應用程式有很多動態 Entity SQL 查詢，則使用查詢來填入快取，也會在從快取中排清時，有效地導致 CompiledQueries 「反編譯」。</span><span class="sxs-lookup"><span data-stu-id="820d5-432">In fact, if an app has lots of dynamic Entity SQL queries, filling the cache with queries will also effectively cause CompiledQueries to “decompile” when they are flushed from the cache.</span></span> <span data-ttu-id="820d5-433">在此案例中，您可以藉由停用動態查詢的快取來設定 CompiledQueries 的優先順序，藉此改善效能。</span><span class="sxs-lookup"><span data-stu-id="820d5-433">In this scenario, performance may be improved by disabling caching on the dynamic queries to prioritize the CompiledQueries.</span></span> <span data-ttu-id="820d5-434">更棒的是，要重寫應用程式以使用參數化查詢，而不是動態查詢。</span><span class="sxs-lookup"><span data-stu-id="820d5-434">Better yet, of course, would be to rewrite the app to use parameterized queries instead of dynamic queries.</span></span>

### <a name="33-using-compiledquery-to-improve-performance-with-linq-queries"></a><span data-ttu-id="820d5-435">3.3 使用 CompiledQuery 來改善 LINQ 查詢的效能</span><span class="sxs-lookup"><span data-stu-id="820d5-435">3.3 Using CompiledQuery to improve performance with LINQ queries</span></span>

<span data-ttu-id="820d5-436">我們的測試指出使用 CompiledQuery 可享有 7% over autocompiled LINQ 查詢的優點;這表示您會花費7% 的時間來執行 Entity Framework 堆疊的程式碼;這並不表示您的應用程式速度將會加快7%。</span><span class="sxs-lookup"><span data-stu-id="820d5-436">Our tests indicate that using CompiledQuery can bring a benefit of 7% over autocompiled LINQ queries; this means that you’ll spend 7% less time executing code from the Entity Framework stack; it does not mean your application will be 7% faster.</span></span> <span data-ttu-id="820d5-437">一般來說，相較于優點，在 EF 5.0 中寫入和維護 CompiledQuery 物件的成本可能不是值得一提的問題。</span><span class="sxs-lookup"><span data-stu-id="820d5-437">Generally speaking, the cost of writing and maintaining CompiledQuery objects in EF 5.0 may not be worth the trouble when compared to the benefits.</span></span> <span data-ttu-id="820d5-438">您的里程可能會有所不同，因此如果您的專案需要額外的推送，請執行此選項。</span><span class="sxs-lookup"><span data-stu-id="820d5-438">Your mileage may vary, so exercise this option if your project requires the extra push.</span></span> <span data-ttu-id="820d5-439">請注意，CompiledQueries 只與 ObjectCoNtext 衍生模型相容，而且與 DbCoNtext 衍生模型不相容。</span><span class="sxs-lookup"><span data-stu-id="820d5-439">Note that CompiledQueries are only compatible with ObjectContext-derived models, and not compatible with DbContext-derived models.</span></span>

<span data-ttu-id="820d5-440">如需有關建立和叫用 CompiledQuery 的詳細資訊，請參閱[編譯的查詢（LINQ to Entities）](https://msdn.microsoft.com/library/bb896297.aspx)。</span><span class="sxs-lookup"><span data-stu-id="820d5-440">For more information on creating and invoking a CompiledQuery, see [Compiled Queries (LINQ to Entities)](https://msdn.microsoft.com/library/bb896297.aspx).</span></span>

<span data-ttu-id="820d5-441">使用 CompiledQuery 時，您必須採取兩個考慮，也就是使用靜態實例的需求，以及它們與複合性的問題。</span><span class="sxs-lookup"><span data-stu-id="820d5-441">There are two considerations you have to take when using a CompiledQuery, namely the requirement to use static instances and the problems they have with composability.</span></span> <span data-ttu-id="820d5-442">以下是這兩個考慮的深入說明。</span><span class="sxs-lookup"><span data-stu-id="820d5-442">Here follows an in-depth explanation of these two considerations.</span></span>

#### <a name="331-use-static-compiledquery-instances"></a><span data-ttu-id="820d5-443">3.3.1 使用靜態 CompiledQuery 實例</span><span class="sxs-lookup"><span data-stu-id="820d5-443">3.3.1       Use static CompiledQuery instances</span></span>

<span data-ttu-id="820d5-444">因為編譯 LINQ 查詢是非常耗時的程式，所以我們不想要在每次需要從資料庫提取資料時執行此作業。</span><span class="sxs-lookup"><span data-stu-id="820d5-444">Since compiling a LINQ query is a time-consuming process, we don’t want to do it every time we need to fetch data from the database.</span></span> <span data-ttu-id="820d5-445">CompiledQuery 實例可讓您編譯一次並執行多次，但您必須謹慎和採購，以便每次都重複使用相同的 CompiledQuery 實例，而不是重新編譯。</span><span class="sxs-lookup"><span data-stu-id="820d5-445">CompiledQuery instances allow you to compile once and run multiple times, but you have to be careful and procure to re-use the same CompiledQuery instance every time instead of compiling it over and over again.</span></span> <span data-ttu-id="820d5-446">若要使用靜態成員來儲存 CompiledQuery 實例，就必須這麼做;否則，您將不會看到任何權益。</span><span class="sxs-lookup"><span data-stu-id="820d5-446">The use of static members to store the CompiledQuery instances becomes necessary; otherwise you won’t see any benefit.</span></span>

<span data-ttu-id="820d5-447">例如，假設您的頁面具有下列方法主體，可處理顯示所選類別目錄的產品：</span><span class="sxs-lookup"><span data-stu-id="820d5-447">For example, suppose your page has the following method body to handle displaying the products for the selected category:</span></span>

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

<span data-ttu-id="820d5-448">在此情況下，您會在每次呼叫方法時，即時建立新的 CompiledQuery 實例。</span><span class="sxs-lookup"><span data-stu-id="820d5-448">In this case, you will create a new CompiledQuery instance on the fly every time the method is called.</span></span> <span data-ttu-id="820d5-449">CompiledQuery 會在每次建立新的實例時通過計畫編譯器，而不是從查詢計劃快取中抓取 store 命令來查看效能優勢。</span><span class="sxs-lookup"><span data-stu-id="820d5-449">Instead of seeing performance benefits by retrieving the store command from the query plan cache, the CompiledQuery will go through the plan compiler every time a new instance is created.</span></span> <span data-ttu-id="820d5-450">事實上，每次呼叫方法時，您都會以新的 CompiledQuery 專案來污染您的查詢計劃快取。</span><span class="sxs-lookup"><span data-stu-id="820d5-450">In fact, you will be polluting your query plan cache with a new CompiledQuery entry every time the method is called.</span></span>

<span data-ttu-id="820d5-451">相反地，您會想要建立已編譯查詢的靜態實例，因此每次呼叫方法時，都會叫用相同的已編譯查詢。</span><span class="sxs-lookup"><span data-stu-id="820d5-451">Instead, you want to create a static instance of the compiled query, so you are invoking the same compiled query every time the method is called.</span></span> <span data-ttu-id="820d5-452">這麼做的方法之一，是將 CompiledQuery 實例新增為物件內容的成員。</span><span class="sxs-lookup"><span data-stu-id="820d5-452">One way to so this is by adding the CompiledQuery instance as a member of your object context.</span></span><span data-ttu-id="820d5-453">  接著，您可以透過 helper 方法來存取 CompiledQuery，讓事情更清楚明瞭：</span><span class="sxs-lookup"><span data-stu-id="820d5-453">  You can then make things a little cleaner by accessing the CompiledQuery through a helper method:</span></span>

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

<span data-ttu-id="820d5-454">此 helper 方法的叫用方式如下：</span><span class="sxs-lookup"><span data-stu-id="820d5-454">This helper method would be invoked as follows:</span></span>

``` csharp
    this.productsGrid.DataSource = context.GetProductsForCategory(selectedCategory);
```

#### <a name="332-composing-over-a-compiledquery"></a><span data-ttu-id="820d5-455">3.3.2 在 CompiledQuery 上撰寫</span><span class="sxs-lookup"><span data-stu-id="820d5-455">3.3.2       Composing over a CompiledQuery</span></span>

<span data-ttu-id="820d5-456">撰寫任何 LINQ 查詢的功能非常有用;若要這樣做，您只需在 IQueryable 之後叫用方法，例如*Skip （）* 或*Count （）* 。</span><span class="sxs-lookup"><span data-stu-id="820d5-456">The ability to compose over any LINQ query is extremely useful; to do this, you simply invoke a method after the IQueryable such as *Skip()* or *Count()*.</span></span> <span data-ttu-id="820d5-457">不過，這麼做基本上會傳回新的 IQueryable 物件。</span><span class="sxs-lookup"><span data-stu-id="820d5-457">However, doing so essentially returns a new IQueryable object.</span></span> <span data-ttu-id="820d5-458">雖然在技術上不會讓您撰寫 CompiledQuery，但這樣做會產生新的 IQueryable 物件，而需要再次傳遞計畫編譯器。</span><span class="sxs-lookup"><span data-stu-id="820d5-458">While there’s nothing to stop you technically from composing over a CompiledQuery, doing so will cause the generation of a new IQueryable object that requires passing through the plan compiler again.</span></span>

<span data-ttu-id="820d5-459">有些元件會利用組合的 IQueryable 物件來啟用先進的功能。</span><span class="sxs-lookup"><span data-stu-id="820d5-459">Some components will make use of composed IQueryable objects to enable advanced functionality.</span></span> <span data-ttu-id="820d5-460">例如，ASP。NET 的 GridView 可以透過 SelectMethod 屬性，透過資料系結至 IQueryable 物件。</span><span class="sxs-lookup"><span data-stu-id="820d5-460">For example, ASP.NET’s GridView can be data-bound to an IQueryable object via the SelectMethod property.</span></span> <span data-ttu-id="820d5-461">然後 GridView 會透過此 IQueryable 物件撰寫，以允許對資料模型進行排序和分頁。</span><span class="sxs-lookup"><span data-stu-id="820d5-461">The GridView will then compose over this IQueryable object to allow sorting and paging over the data model.</span></span> <span data-ttu-id="820d5-462">如您所見，使用 GridView 的 CompiledQuery 並不會叫用編譯過的查詢，而是會產生新的 autocompiled 查詢。</span><span class="sxs-lookup"><span data-stu-id="820d5-462">As you can see, using a CompiledQuery for the GridView would not hit the compiled query but would generate a new autocompiled query.</span></span>

<span data-ttu-id="820d5-463">您可能遇到這種情況的一個地方就是將漸進式篩選加入查詢時。</span><span class="sxs-lookup"><span data-stu-id="820d5-463">One place where you may run into this is when adding progressive filters to a query.</span></span> <span data-ttu-id="820d5-464">例如，假設您有一個 [客戶] 頁面，其中包含多個選擇性篩選的下拉式清單（例如 Country 和 OrdersCount）。</span><span class="sxs-lookup"><span data-stu-id="820d5-464">For example, suppose you had a Customers page with several drop-down lists for optional filters (for example, Country and OrdersCount).</span></span> <span data-ttu-id="820d5-465">您可以對 CompiledQuery 的 IQueryable 結果撰寫這些篩選器，但這樣做會導致新的查詢在每次執行時都通過計畫編譯器。</span><span class="sxs-lookup"><span data-stu-id="820d5-465">You can compose these filters over the IQueryable results of a CompiledQuery, but doing so will result in the new query going through the plan compiler every time you execute it.</span></span>

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

 <span data-ttu-id="820d5-466">若要避免這種重新編譯，您可以重寫 CompiledQuery，將可能的篩選準則納入考慮：</span><span class="sxs-lookup"><span data-stu-id="820d5-466">To avoid this re-compilation, you can rewrite the CompiledQuery to take the possible filters into account:</span></span>

``` csharp
    private static readonly Func<NorthwindEntities, int, int?, string, IQueryable<Customer>> customersForEmployeeWithFiltersCQ = CompiledQuery.Compile(
        (NorthwindEntities context, int empId, int? countFilter, string countryFilter) =>
            context.Customers.Where(c => c.Orders.Any(o => o.EmployeeID == empId))
            .Where(c => countFilter.HasValue == false || c.Orders.Count > countFilter)
            .Where(c => countryFilter == null || c.Address.Country == countryFilter)
        );
```

<span data-ttu-id="820d5-467">這會在 UI 中叫用，如下所示：</span><span class="sxs-lookup"><span data-stu-id="820d5-467">Which would be invoked in the UI like:</span></span>

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

 <span data-ttu-id="820d5-468">這裡的缺點是，產生的存放區命令一律會有具有 null 檢查的篩選準則，但資料庫伺服器要優化這些動作應該相當簡單：</span><span class="sxs-lookup"><span data-stu-id="820d5-468">A tradeoff here is the generated store command will always have the filters with the null checks, but these should be fairly simple for the database server to optimize:</span></span>

``` SQL
...
WHERE ((0 = (CASE WHEN (@p__linq__1 IS NOT NULL) THEN cast(1 as bit) WHEN (@p__linq__1 IS NULL) THEN cast(0 as bit) END)) OR ([Project3].[C2] > @p__linq__2)) AND (@p__linq__3 IS NULL OR [Project3].[Country] = @p__linq__4)
```

### <a name="34-metadata-caching"></a><span data-ttu-id="820d5-469">3.4 中繼資料快取</span><span class="sxs-lookup"><span data-stu-id="820d5-469">3.4 Metadata caching</span></span>

<span data-ttu-id="820d5-470">Entity Framework 也支援中繼資料快取。</span><span class="sxs-lookup"><span data-stu-id="820d5-470">The Entity Framework also supports Metadata caching.</span></span> <span data-ttu-id="820d5-471">這基本上是快取型別資訊，以及與相同模型的不同連接之間的型別對資料庫對應資訊。</span><span class="sxs-lookup"><span data-stu-id="820d5-471">This is essentially caching of type information and type-to-database mapping information across different connections to the same model.</span></span> <span data-ttu-id="820d5-472">中繼資料快取在每個 AppDomain 中都是唯一的。</span><span class="sxs-lookup"><span data-stu-id="820d5-472">The Metadata cache is unique per AppDomain.</span></span>

#### <a name="341-metadata-caching-algorithm"></a><span data-ttu-id="820d5-473">3.4.1 中繼資料快取演算法</span><span class="sxs-lookup"><span data-stu-id="820d5-473">3.4.1 Metadata Caching algorithm</span></span>

1.  <span data-ttu-id="820d5-474">模型的中繼資料資訊會儲存在每個 EntityConnection 的 ItemCollection 中。</span><span class="sxs-lookup"><span data-stu-id="820d5-474">Metadata information for a model is stored in an ItemCollection for each EntityConnection.</span></span>
    -   <span data-ttu-id="820d5-475">請注意，模型的不同部分會有不同的 ItemCollection 物件。</span><span class="sxs-lookup"><span data-stu-id="820d5-475">As a side note, there are different ItemCollection objects for different parts of the model.</span></span> <span data-ttu-id="820d5-476">例如，StoreItemCollections 包含資料庫模型的相關資訊。ObjectItemCollection 包含資料模型的相關資訊;EdmItemCollection 包含概念模型的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="820d5-476">For example, StoreItemCollections contains the information about the database model; ObjectItemCollection contains information about the data model; EdmItemCollection contains information about the conceptual model.</span></span>

2.  <span data-ttu-id="820d5-477">如果兩個連接使用相同的連接字串，則會共用相同的 ItemCollection 實例。</span><span class="sxs-lookup"><span data-stu-id="820d5-477">If two connections use the same connection string, they will share the same ItemCollection instance.</span></span>
3.  <span data-ttu-id="820d5-478">功能相同但以不同的方式連接字串，可能會導致不同的中繼資料快取。</span><span class="sxs-lookup"><span data-stu-id="820d5-478">Functionally equivalent but textually different connection strings may result in different metadata caches.</span></span> <span data-ttu-id="820d5-479">我們會 token 化連接字串，因此只要變更權杖的順序，就會產生共用的中繼資料。</span><span class="sxs-lookup"><span data-stu-id="820d5-479">We do tokenize connection strings, so simply changing the order of the tokens should result in shared metadata.</span></span> <span data-ttu-id="820d5-480">但是看起來相同的兩個連接字串在 token 化之後可能不會被評估為相同。</span><span class="sxs-lookup"><span data-stu-id="820d5-480">But two connection strings that seem functionally the same may not be evaluated as identical after tokenization.</span></span>
4.  <span data-ttu-id="820d5-481">ItemCollection 會定期檢查以供使用。</span><span class="sxs-lookup"><span data-stu-id="820d5-481">The ItemCollection is periodically checked for use.</span></span> <span data-ttu-id="820d5-482">如果判斷出最近尚未存取的工作區，則會在下一次快取清除時將其標示為清理。</span><span class="sxs-lookup"><span data-stu-id="820d5-482">If it is determined that a workspace has not been accessed recently, it will be marked for cleanup on the next cache sweep.</span></span>
5.  <span data-ttu-id="820d5-483">只要建立 EntityConnection，就會建立中繼資料快取（不過，在開啟連接之前，其中的專案集合將不會初始化）。</span><span class="sxs-lookup"><span data-stu-id="820d5-483">Merely creating an EntityConnection will cause a metadata cache to be created (though the item collections in it will not be initialized until the connection is opened).</span></span> <span data-ttu-id="820d5-484">此工作區會保留在記憶體中，直到快取演算法判斷其不是「使用中」為止。</span><span class="sxs-lookup"><span data-stu-id="820d5-484">This workspace will remain in-memory until the caching algorithm determines it is not “in use”.</span></span>

<span data-ttu-id="820d5-485">客戶諮詢小組已撰寫一篇 blog 文章，其中描述保留對 ItemCollection 的參考，以避免在使用大型模型時「淘汰」： \<http://blogs.msdn.com/b/appfabriccat/archive/2010/10/22/metadataworkspace-reference-in-wcf-services.aspx>。</span><span class="sxs-lookup"><span data-stu-id="820d5-485">The Customer Advisory Team has written a blog post that describes holding a reference to an ItemCollection in order to avoid "deprecation" when using large models: \<http://blogs.msdn.com/b/appfabriccat/archive/2010/10/22/metadataworkspace-reference-in-wcf-services.aspx>.</span></span>

#### <a name="342-the-relationship-between-metadata-caching-and-query-plan-caching"></a><span data-ttu-id="820d5-486">3.4.2 中繼資料快取與查詢計劃快取之間的關聯性</span><span class="sxs-lookup"><span data-stu-id="820d5-486">3.4.2 The relationship between Metadata Caching and Query Plan Caching</span></span>

<span data-ttu-id="820d5-487">查詢計劃快取實例存在於 MetadataWorkspace 的存放區類型 ItemCollection 中。</span><span class="sxs-lookup"><span data-stu-id="820d5-487">The query plan cache instance lives in the MetadataWorkspace's ItemCollection of store types.</span></span> <span data-ttu-id="820d5-488">這表示會使用快取的存放區命令，針對使用指定 MetadataWorkspace 具現化的任何內容進行查詢。</span><span class="sxs-lookup"><span data-stu-id="820d5-488">This means that cached store commands will be used for queries against any context instantiated using a given MetadataWorkspace.</span></span> <span data-ttu-id="820d5-489">這也表示，如果您的兩個連接字串稍有不同，而且在 token 化之後並不相符，您將會有不同的查詢計劃快取實例。</span><span class="sxs-lookup"><span data-stu-id="820d5-489">It also means that if you have two connections strings that are slightly different and don't match after tokenizing, you will have different query plan cache instances.</span></span>

### <a name="35-results-caching"></a><span data-ttu-id="820d5-490">3.5 結果快取</span><span class="sxs-lookup"><span data-stu-id="820d5-490">3.5 Results caching</span></span>

<span data-ttu-id="820d5-491">使用結果快取（也稱為「第二層快取」），您可以將查詢的結果保留在本機快取中。</span><span class="sxs-lookup"><span data-stu-id="820d5-491">With results caching (also known as "second-level caching"), you keep the results of queries in a local cache.</span></span> <span data-ttu-id="820d5-492">發出查詢時，您會先看到結果是否可在本機上使用，然後再對存放區進行查詢。</span><span class="sxs-lookup"><span data-stu-id="820d5-492">When issuing a query, you first see if the results are available locally before you query against the store.</span></span> <span data-ttu-id="820d5-493">雖然 Entity Framework 不直接支援結果快取，但可以使用包裝提供者來新增第二層快取。</span><span class="sxs-lookup"><span data-stu-id="820d5-493">While results caching isn't directly supported by Entity Framework, it's possible to add a second level cache by using a wrapping provider.</span></span> <span data-ttu-id="820d5-494">使用第二層快取的範例包裝提供者是 Alachisoft 的[Entity Framework 第二層](https://www.alachisoft.com/ncache/entity-framework.html)快取（以 NCache 為基礎）。</span><span class="sxs-lookup"><span data-stu-id="820d5-494">An example wrapping provider with a second-level cache is Alachisoft's [Entity Framework Second Level Cache based on NCache](https://www.alachisoft.com/ncache/entity-framework.html).</span></span>

<span data-ttu-id="820d5-495">第二層快取的執行是插入的功能，它會在評估 LINQ 運算式之後發生（和 funcletized），並從第一層快取計算或抓取查詢執行計畫。</span><span class="sxs-lookup"><span data-stu-id="820d5-495">This implementation of second-level caching is an injected functionality that takes place after the LINQ expression has been evaluated (and funcletized) and the query execution plan is computed or retrieved from the first-level cache.</span></span> <span data-ttu-id="820d5-496">第二層快取只會儲存原始資料庫結果，因此具體化管線之後仍會執行。</span><span class="sxs-lookup"><span data-stu-id="820d5-496">The second-level cache will then store only the raw database results, so the materialization pipeline still executes afterwards.</span></span>

#### <a name="351-additional-references-for-results-caching-with-the-wrapping-provider"></a><span data-ttu-id="820d5-497">使用包裝提供者3.5.1 結果快取的其他參考</span><span class="sxs-lookup"><span data-stu-id="820d5-497">3.5.1 Additional references for results caching with the wrapping provider</span></span>

-   <span data-ttu-id="820d5-498">Julie Lerman 已在 Entity Framework 和 Windows Azure 中撰寫了「第二層快取」 MSDN 文章，其中包括如何更新範例包裝提供者以使用 Windows Server AppFabric 快取： [https://msdn.microsoft.com/magazine/hh394143.aspx](https://msdn.microsoft.com/magazine/hh394143.aspx)</span><span class="sxs-lookup"><span data-stu-id="820d5-498">Julie Lerman has written a "Second-Level Caching in Entity Framework and Windows Azure" MSDN article that includes how to update the sample wrapping provider to use Windows Server AppFabric caching: [https://msdn.microsoft.com/magazine/hh394143.aspx](https://msdn.microsoft.com/magazine/hh394143.aspx)</span></span>
-   <span data-ttu-id="820d5-499">如果您使用 Entity Framework 5，則小組的 blog 會有一篇文章，說明如何使用快取提供者（Entity Framework 5： \<http://blogs.msdn.com/b/adonet/archive/2010/09/13/ef-caching-with-jarek-kowalski-s-provider.aspx>）來執行專案。</span><span class="sxs-lookup"><span data-stu-id="820d5-499">If you are working with Entity Framework 5, the team blog has a post that describes how to get things running with the caching provider for Entity Framework 5: \<http://blogs.msdn.com/b/adonet/archive/2010/09/13/ef-caching-with-jarek-kowalski-s-provider.aspx>.</span></span> <span data-ttu-id="820d5-500">它也包含 T4 範本，可協助自動化將第二層快取新增至您的專案。</span><span class="sxs-lookup"><span data-stu-id="820d5-500">It also includes a T4 template to help automate adding the 2nd-level caching to your project.</span></span>

## <a name="4-autocompiled-queries"></a><span data-ttu-id="820d5-501">4 Autocompiled 查詢</span><span class="sxs-lookup"><span data-stu-id="820d5-501">4 Autocompiled Queries</span></span>

<span data-ttu-id="820d5-502">使用 Entity Framework 針對資料庫發出查詢時，必須經過一系列的步驟，才能實際將結果具體化。其中一個步驟是查詢編譯。</span><span class="sxs-lookup"><span data-stu-id="820d5-502">When a query is issued against a database using Entity Framework, it must go through a series of steps before actually materializing the results; one such step is Query Compilation.</span></span> <span data-ttu-id="820d5-503">已知 Entity SQL 查詢會有良好的效能，因為它們會自動快取，因此當您執行相同的查詢時，第二個或第三次會略過計畫編譯器，並改為使用快取的計畫。</span><span class="sxs-lookup"><span data-stu-id="820d5-503">Entity SQL queries were known to have good performance as they are automatically cached, so the second or third time you execute the same query it can skip the plan compiler and use the cached plan instead.</span></span>

<span data-ttu-id="820d5-504">Entity Framework 5 也為 LINQ to Entities 的查詢引進了自動快取。</span><span class="sxs-lookup"><span data-stu-id="820d5-504">Entity Framework 5 introduced automatic caching for LINQ to Entities queries as well.</span></span> <span data-ttu-id="820d5-505">在過去的版本中 Entity Framework 建立 CompiledQuery 來加速效能是常見的作法，因為這會讓您的 LINQ to Entities 查詢得以快取。</span><span class="sxs-lookup"><span data-stu-id="820d5-505">In past editions of Entity Framework creating a CompiledQuery to speed your performance was a common practice, as this would make your LINQ to Entities query cacheable.</span></span> <span data-ttu-id="820d5-506">因為快取現在會自動完成，而不使用 CompiledQuery，所以我們稱之為「autocompiled 查詢」這項功能。</span><span class="sxs-lookup"><span data-stu-id="820d5-506">Since caching is now done automatically without the use of a CompiledQuery, we call this feature “autocompiled queries”.</span></span> <span data-ttu-id="820d5-507">如需查詢計劃快取和其機制的詳細資訊，請參閱查詢計劃快取。</span><span class="sxs-lookup"><span data-stu-id="820d5-507">For more information about the query plan cache and its mechanics, see Query Plan Caching.</span></span>

<span data-ttu-id="820d5-508">Entity Framework 會偵測何時需要重新編譯查詢，並在叫用查詢時執行，即使先前已編譯過。</span><span class="sxs-lookup"><span data-stu-id="820d5-508">Entity Framework detects when a query requires to be recompiled, and does so when the query is invoked even if it had been compiled before.</span></span> <span data-ttu-id="820d5-509">導致重新編譯查詢的常見狀況如下：</span><span class="sxs-lookup"><span data-stu-id="820d5-509">Common conditions that cause the query to be recompiled are:</span></span>

-   <span data-ttu-id="820d5-510">變更與查詢相關聯的 MergeOption。</span><span class="sxs-lookup"><span data-stu-id="820d5-510">Changing the MergeOption associated to your query.</span></span> <span data-ttu-id="820d5-511">將不會使用快取的查詢，而是會重新執行計畫編譯器，並快取新建立的計畫。</span><span class="sxs-lookup"><span data-stu-id="820d5-511">The cached query will not be used, instead the plan compiler will run again and the newly created plan gets cached.</span></span>
-   <span data-ttu-id="820d5-512">變更 CoNtextoptions.negotiate. UseCSharpNullComparisonBehavior 的值。</span><span class="sxs-lookup"><span data-stu-id="820d5-512">Changing the value of ContextOptions.UseCSharpNullComparisonBehavior.</span></span> <span data-ttu-id="820d5-513">您會得到與變更 MergeOption 相同的效果。</span><span class="sxs-lookup"><span data-stu-id="820d5-513">You get the same effect as changing the MergeOption.</span></span>

<span data-ttu-id="820d5-514">其他條件可能會讓您的查詢無法使用快取。</span><span class="sxs-lookup"><span data-stu-id="820d5-514">Other conditions can prevent your query from using the cache.</span></span> <span data-ttu-id="820d5-515">常見的範例包括：</span><span class="sxs-lookup"><span data-stu-id="820d5-515">Common examples are:</span></span>

-   <span data-ttu-id="820d5-516">使用 IEnumerable&lt;T&gt;。包含&lt;&gt;（T 值）。</span><span class="sxs-lookup"><span data-stu-id="820d5-516">Using IEnumerable&lt;T&gt;.Contains&lt;&gt;(T value).</span></span>
-   <span data-ttu-id="820d5-517">使用會產生具有常數之查詢的函數。</span><span class="sxs-lookup"><span data-stu-id="820d5-517">Using functions that produce queries with constants.</span></span>
-   <span data-ttu-id="820d5-518">使用非對應物件的屬性。</span><span class="sxs-lookup"><span data-stu-id="820d5-518">Using the properties of a non-mapped object.</span></span>
-   <span data-ttu-id="820d5-519">將您的查詢連結到需要重新編譯的另一個查詢。</span><span class="sxs-lookup"><span data-stu-id="820d5-519">Linking your query to another query that requires to be recompiled.</span></span>

### <a name="41-using-ienumerablelttgtcontainslttgtt-value"></a><span data-ttu-id="820d5-520">4.1 使用 IEnumerable&lt;T&gt;。包含&lt;T&gt;（T 值）</span><span class="sxs-lookup"><span data-stu-id="820d5-520">4.1 Using IEnumerable&lt;T&gt;.Contains&lt;T&gt;(T value)</span></span>

<span data-ttu-id="820d5-521">Entity Framework 不會快取叫用 IEnumerable&lt;T&gt;的查詢。針對記憶體中的集合包含&lt;T&gt;（T 值），因為集合的值會被視為 volatile。</span><span class="sxs-lookup"><span data-stu-id="820d5-521">Entity Framework does not cache queries that invoke IEnumerable&lt;T&gt;.Contains&lt;T&gt;(T value) against an in-memory collection, since the values of the collection are considered volatile.</span></span> <span data-ttu-id="820d5-522">下列範例查詢將不會快取，因此它一律會由計畫編譯器進行處理：</span><span class="sxs-lookup"><span data-stu-id="820d5-522">The following example query will not be cached, so it will always be processed by the plan compiler:</span></span>

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

<span data-ttu-id="820d5-523">請注意，執行包含的 IEnumerable 大小會決定查詢的速度或速度。</span><span class="sxs-lookup"><span data-stu-id="820d5-523">Note that the size of the IEnumerable against which Contains is executed determines how fast or how slow your query is compiled.</span></span> <span data-ttu-id="820d5-524">使用大型集合（如上述範例所示）時，效能可能會大幅降低。</span><span class="sxs-lookup"><span data-stu-id="820d5-524">Performance can suffer significantly when using large collections such as the one shown in the example above.</span></span>

<span data-ttu-id="820d5-525">Entity Framework 6 包含 IEnumerable&lt;T&gt;方式的優化。包含&lt;T&gt;（T 值）可在執行查詢時運作。</span><span class="sxs-lookup"><span data-stu-id="820d5-525">Entity Framework 6 contains optimizations to the way IEnumerable&lt;T&gt;.Contains&lt;T&gt;(T value) works when queries are executed.</span></span> <span data-ttu-id="820d5-526">產生的 SQL 程式碼速度會更快，且更容易閱讀，而且在大部分情況下，在伺服器中執行的速度也會更快。</span><span class="sxs-lookup"><span data-stu-id="820d5-526">The SQL code that is generated is much faster to produce and more readable, and in most cases it also executes faster in the server.</span></span>

### <a name="42-using-functions-that-produce-queries-with-constants"></a><span data-ttu-id="820d5-527">4.2 使用會產生常數查詢的函式</span><span class="sxs-lookup"><span data-stu-id="820d5-527">4.2 Using functions that produce queries with constants</span></span>

<span data-ttu-id="820d5-528">Skip （）、Take （）、Contains （）和 DefautIfEmpty （） LINQ 運算子不會產生具有參數的 SQL 查詢，而是將值傳遞給它們做為常數。</span><span class="sxs-lookup"><span data-stu-id="820d5-528">The Skip(), Take(), Contains() and DefautIfEmpty() LINQ operators do not produce SQL queries with parameters but instead put the values passed to them as constants.</span></span> <span data-ttu-id="820d5-529">因此，可能是完全相同的查詢最後會污染查詢計劃快取（在 EF 堆疊和資料庫伺服器上），而且除非在後續的查詢執行中使用相同的常數，否則不會取得 reutilized。</span><span class="sxs-lookup"><span data-stu-id="820d5-529">Because of this, queries that might otherwise be identical end up polluting the query plan cache, both on the EF stack and on the database server, and do not get reutilized unless the same constants are used in a subsequent query execution.</span></span> <span data-ttu-id="820d5-530">例如：</span><span class="sxs-lookup"><span data-stu-id="820d5-530">For example:</span></span>

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

<span data-ttu-id="820d5-531">在此範例中，每次執行此查詢時，都會有不同的 id 值，查詢將會編譯成新的計畫。</span><span class="sxs-lookup"><span data-stu-id="820d5-531">In this example, each time this query is executed with a different value for id the query will be compiled into a new plan.</span></span>

<span data-ttu-id="820d5-532">特別注意，在進行分頁時使用 Skip 和 Take。</span><span class="sxs-lookup"><span data-stu-id="820d5-532">In particular pay attention to the use of Skip and Take when doing paging.</span></span> <span data-ttu-id="820d5-533">在 EF6 中，這些方法有一個 lambda 多載，可有效地讓快取的查詢計劃可重複使用，因為 EF 可以捕捉傳遞給這些方法的變數，並將它們轉譯成 SQLparameters。</span><span class="sxs-lookup"><span data-stu-id="820d5-533">In EF6 these methods have a lambda overload that effectively makes the cached query plan reusable because EF can capture variables passed to these methods and translate them to SQLparameters.</span></span> <span data-ttu-id="820d5-534">這也有助於保留快取清除工具，因為否則 Skip 和 Take 的每個查詢都有不同的常數會取得自己的查詢計劃快取專案。</span><span class="sxs-lookup"><span data-stu-id="820d5-534">This also helps keep the cache cleaner since otherwise each query with a different constant for Skip and Take would get its own query plan cache entry.</span></span>

<span data-ttu-id="820d5-535">請考慮下列程式碼，這是最理想的，但僅適用于範例說明點此類別的查詢：</span><span class="sxs-lookup"><span data-stu-id="820d5-535">Consider the following code, which is suboptimal but is only meant to exemplify this class of queries:</span></span>

``` csharp
var customers = context.Customers.OrderBy(c => c.LastName);
for (var i = 0; i < count; ++i)
{
    var currentCustomer = customers.Skip(i).FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

<span data-ttu-id="820d5-536">這個相同程式碼的更快速版本會牽涉到使用 lambda 呼叫 Skip：</span><span class="sxs-lookup"><span data-stu-id="820d5-536">A faster version of this same code would involve calling Skip with a lambda:</span></span>

``` csharp
var customers = context.Customers.OrderBy(c => c.LastName);
for (var i = 0; i < count; ++i)
{
    var currentCustomer = customers.Skip(() => i).FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

<span data-ttu-id="820d5-537">第二個程式碼片段的執行速度最多可達11%，因為每次執行查詢時都會使用相同的查詢計劃，這樣可節省 CPU 時間並避免查詢快取污染。</span><span class="sxs-lookup"><span data-stu-id="820d5-537">The second snippet may run up to 11% faster because the same query plan is used every time the query is run, which saves CPU time and avoids polluting the query cache.</span></span> <span data-ttu-id="820d5-538">此外，因為要略過的參數是在結束時，程式碼現在可能也會如下所示：</span><span class="sxs-lookup"><span data-stu-id="820d5-538">Furthermore, because the parameter to Skip is in a closure the code might as well look like this now:</span></span>

``` csharp
var i = 0;
var skippyCustomers = context.Customers.OrderBy(c => c.LastName).Skip(() => i);
for (; i < count; ++i)
{
    var currentCustomer = skippyCustomers.FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

### <a name="43-using-the-properties-of-a-non-mapped-object"></a><span data-ttu-id="820d5-539">4.3 使用非對應物件的屬性</span><span class="sxs-lookup"><span data-stu-id="820d5-539">4.3 Using the properties of a non-mapped object</span></span>

<span data-ttu-id="820d5-540">當查詢使用非對應物件類型的屬性做為參數時，查詢將不會被快取。</span><span class="sxs-lookup"><span data-stu-id="820d5-540">When a query uses the properties of a non-mapped object type as a parameter then the query will not get cached.</span></span> <span data-ttu-id="820d5-541">例如：</span><span class="sxs-lookup"><span data-stu-id="820d5-541">For example:</span></span>

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

<span data-ttu-id="820d5-542">在此範例中，假設類別 NonMappedType 不是實體模型的一部分。</span><span class="sxs-lookup"><span data-stu-id="820d5-542">In this example, assume that class NonMappedType is not part of the Entity model.</span></span> <span data-ttu-id="820d5-543">您可以輕鬆地將此查詢變更為不使用非對應的類型，並改為使用區域變數做為查詢的參數：</span><span class="sxs-lookup"><span data-stu-id="820d5-543">This query can easily be changed to not use a non-mapped type and instead use a local variable as the parameter to the query:</span></span>

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

<span data-ttu-id="820d5-544">在此情況下，查詢將能夠進行快取，而且將受益于查詢計劃快取。</span><span class="sxs-lookup"><span data-stu-id="820d5-544">In this case, the query will be able to get cached and will benefit from the query plan cache.</span></span>

### <a name="44-linking-to-queries-that-require-recompiling"></a><span data-ttu-id="820d5-545">4.4 連結至需要重新編譯的查詢</span><span class="sxs-lookup"><span data-stu-id="820d5-545">4.4 Linking to queries that require recompiling</span></span>

<span data-ttu-id="820d5-546">遵循與上述相同的範例，如果您有第二個查詢需要重新編譯的查詢，則整個第二個查詢也會重新編譯。</span><span class="sxs-lookup"><span data-stu-id="820d5-546">Following the same example as above, if you have a second query that relies on a query that needs to be recompiled, your entire second query will also be recompiled.</span></span> <span data-ttu-id="820d5-547">以下是說明此案例的範例：</span><span class="sxs-lookup"><span data-stu-id="820d5-547">Here’s an example to illustrate this scenario:</span></span>

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

<span data-ttu-id="820d5-548">此範例是泛型的，但它會說明連結至 firstQuery 如何導致 secondQuery 無法快取。</span><span class="sxs-lookup"><span data-stu-id="820d5-548">The example is generic, but it illustrates how linking to firstQuery is causing secondQuery to be unable to get cached.</span></span> <span data-ttu-id="820d5-549">如果 firstQuery 尚未是需要重新編譯的查詢，則會快取 secondQuery。</span><span class="sxs-lookup"><span data-stu-id="820d5-549">If firstQuery had not been a query that requires recompiling, then secondQuery would have been cached.</span></span>

## <a name="5-notracking-queries"></a><span data-ttu-id="820d5-550">5個 NoTracking 查詢</span><span class="sxs-lookup"><span data-stu-id="820d5-550">5 NoTracking Queries</span></span>

### <a name="51-disabling-change-tracking-to-reduce-state-management-overhead"></a><span data-ttu-id="820d5-551">5.1 停用變更追蹤以降低狀態管理額外負荷</span><span class="sxs-lookup"><span data-stu-id="820d5-551">5.1 Disabling change tracking to reduce state management overhead</span></span>

<span data-ttu-id="820d5-552">如果您是在唯讀案例中，而且想要避免將物件載入至 ObjectStateManager 的額外負荷，您可以發出「沒有追蹤」查詢。</span><span class="sxs-lookup"><span data-stu-id="820d5-552">If you are in a read-only scenario and want to avoid the overhead of loading the objects into the ObjectStateManager, you can issue "No Tracking" queries.</span></span><span data-ttu-id="820d5-553">  您可以在查詢層級停用變更追蹤。</span><span class="sxs-lookup"><span data-stu-id="820d5-553">  Change tracking can be disabled at the query level.</span></span>

<span data-ttu-id="820d5-554">請注意，藉由停用變更追蹤，您就可以有效地關閉物件快取。</span><span class="sxs-lookup"><span data-stu-id="820d5-554">Note though that by disabling change tracking you are effectively turning off the object cache.</span></span> <span data-ttu-id="820d5-555">當您查詢實體時，我們無法從 ObjectStateManager 提取先前具體化的查詢結果，以略過具體化。</span><span class="sxs-lookup"><span data-stu-id="820d5-555">When you query for an entity, we can't skip materialization by pulling the previously-materialized query results from the ObjectStateManager.</span></span> <span data-ttu-id="820d5-556">如果您要在相同的內容上重複查詢相同的實體，可能實際上會看到啟用變更追蹤的效能優勢。</span><span class="sxs-lookup"><span data-stu-id="820d5-556">If you are repeatedly querying for the same entities on the same context, you might actually see a performance benefit from enabling change tracking.</span></span>

<span data-ttu-id="820d5-557">使用 ObjectCoNtext 進行查詢時，ObjectQuery 和 ObjectSet 實例會在設定好之後記住 MergeOption，而在其上組成的查詢將會繼承父查詢的有效 MergeOption。</span><span class="sxs-lookup"><span data-stu-id="820d5-557">When querying using ObjectContext, ObjectQuery and ObjectSet instances will remember a MergeOption once it is set, and queries that are composed on them will inherit the effective MergeOption of the parent query.</span></span> <span data-ttu-id="820d5-558">使用 DbCoNtext 時，可以藉由呼叫 DbSet 上的 AsNoTracking （）修飾詞來停用追蹤。</span><span class="sxs-lookup"><span data-stu-id="820d5-558">When using DbContext, tracking can be disabled by calling the AsNoTracking() modifier on the DbSet.</span></span>

#### <a name="511-disabling-change-tracking-for-a-query-when-using-dbcontext"></a><span data-ttu-id="820d5-559">5.1.1 使用 DbCoNtext 時停用查詢的變更追蹤</span><span class="sxs-lookup"><span data-stu-id="820d5-559">5.1.1 Disabling change tracking for a query when using DbContext</span></span>

<span data-ttu-id="820d5-560">您可以藉由連結查詢中 AsNoTracking （）方法的呼叫，將查詢的模式切換成 NoTracking。</span><span class="sxs-lookup"><span data-stu-id="820d5-560">You can switch the mode of a query to NoTracking by chaining a call to the AsNoTracking() method in the query.</span></span> <span data-ttu-id="820d5-561">不同于 ObjectQuery，DbCoNtext API 中的 DbSet 和 DbQuery 類別沒有 MergeOption 的可變屬性。</span><span class="sxs-lookup"><span data-stu-id="820d5-561">Unlike ObjectQuery, the DbSet and DbQuery classes in the DbContext API don’t have a mutable property for the MergeOption.</span></span>

``` csharp
    var productsForCategory = from p in context.Products.AsNoTracking()
                                where p.Category.CategoryName == selectedCategory
                                select p;


```

#### <a name="512-disabling-change-tracking-at-the-query-level-using-objectcontext"></a><span data-ttu-id="820d5-562">5.1.2 使用 ObjectCoNtext 在查詢層級停用變更追蹤</span><span class="sxs-lookup"><span data-stu-id="820d5-562">5.1.2 Disabling change tracking at the query level using ObjectContext</span></span>

``` csharp
    var productsForCategory = from p in context.Products
                                where p.Category.CategoryName == selectedCategory
                                select p;

    ((ObjectQuery)productsForCategory).MergeOption = MergeOption.NoTracking;
```

#### <a name="513-disabling-change-tracking-for-an-entire-entity-set-using-objectcontext"></a><span data-ttu-id="820d5-563">5.1.3 使用 ObjectCoNtext 來停用整個實體集的變更追蹤</span><span class="sxs-lookup"><span data-stu-id="820d5-563">5.1.3 Disabling change tracking for an entire entity set using ObjectContext</span></span>

``` csharp
    context.Products.MergeOption = MergeOption.NoTracking;

    var productsForCategory = from p in context.Products
                                where p.Category.CategoryName == selectedCategory
                                select p;
```

### <a name="52test-metrics-demonstrating-the-performance-benefit-of-notracking-queries"></a><span data-ttu-id="820d5-564">5.2 測試計量示範 NoTracking 查詢的效能優點</span><span class="sxs-lookup"><span data-stu-id="820d5-564">5.2 Test Metrics demonstrating the performance benefit of NoTracking queries</span></span>

<span data-ttu-id="820d5-565">在這項測試中，我們將探討藉由比較追蹤與 Navision 模型的 NoTracking 查詢來填滿 ObjectStateManager 的成本。</span><span class="sxs-lookup"><span data-stu-id="820d5-565">In this test we look at the cost of filling the ObjectStateManager by comparing Tracking to NoTracking queries for the Navision model.</span></span> <span data-ttu-id="820d5-566">如需 Navision 模型的描述，以及已執行之查詢的類型，請參閱附錄。</span><span class="sxs-lookup"><span data-stu-id="820d5-566">See the appendix for a description of the Navision model and the types of queries which were executed.</span></span> <span data-ttu-id="820d5-567">在這項測試中，我們會逐一查看查詢清單，並執行一次。</span><span class="sxs-lookup"><span data-stu-id="820d5-567">In this test, we iterate through the list of queries and execute each one once.</span></span> <span data-ttu-id="820d5-568">我們執行了兩種測試變化，一次是使用 NoTracking 查詢，另一次是使用 "AppendOnly" 的預設合併選項。</span><span class="sxs-lookup"><span data-stu-id="820d5-568">We ran two variations of the test, once with NoTracking queries and once with the default merge option of "AppendOnly".</span></span> <span data-ttu-id="820d5-569">我們已執行每個變化3次，並採用執行的平均值。</span><span class="sxs-lookup"><span data-stu-id="820d5-569">We ran each variation 3 times and take the mean value of the runs.</span></span> <span data-ttu-id="820d5-570">在測試之間，我們會清除 SQL Server 上的查詢快取，並執行下列命令來壓縮 tempdb：</span><span class="sxs-lookup"><span data-stu-id="820d5-570">Between the tests we clear the query cache on the SQL Server and shrink the tempdb by running the following commands:</span></span>

1.  <span data-ttu-id="820d5-571">DBCC DROPCLEANBUFFERS</span><span class="sxs-lookup"><span data-stu-id="820d5-571">DBCC DROPCLEANBUFFERS</span></span>
2.  <span data-ttu-id="820d5-572">DBCC FREEPROCCACHE</span><span class="sxs-lookup"><span data-stu-id="820d5-572">DBCC FREEPROCCACHE</span></span>
3.  <span data-ttu-id="820d5-573">DBCC SHRINKDATABASE （tempdb，0）</span><span class="sxs-lookup"><span data-stu-id="820d5-573">DBCC SHRINKDATABASE (tempdb, 0)</span></span>

<span data-ttu-id="820d5-574">測試結果，執行超過3的中間值：</span><span class="sxs-lookup"><span data-stu-id="820d5-574">Test Results, median over 3 runs:</span></span>

|                        | <span data-ttu-id="820d5-575">無追蹤-工作集</span><span class="sxs-lookup"><span data-stu-id="820d5-575">NO TRACKING – WORKING SET</span></span> | <span data-ttu-id="820d5-576">無追蹤–時間</span><span class="sxs-lookup"><span data-stu-id="820d5-576">NO TRACKING – TIME</span></span> | <span data-ttu-id="820d5-577">僅附加-工作集</span><span class="sxs-lookup"><span data-stu-id="820d5-577">APPEND ONLY – WORKING SET</span></span> | <span data-ttu-id="820d5-578">僅附加–時間</span><span class="sxs-lookup"><span data-stu-id="820d5-578">APPEND ONLY – TIME</span></span> |
|:-----------------------|:--------------------------|:-------------------|:--------------------------|:-------------------|
| <span data-ttu-id="820d5-579">**Entity Framework 5**</span><span class="sxs-lookup"><span data-stu-id="820d5-579">**Entity Framework 5**</span></span> | <span data-ttu-id="820d5-580">460361728</span><span class="sxs-lookup"><span data-stu-id="820d5-580">460361728</span></span>                 | <span data-ttu-id="820d5-581">1163536毫秒</span><span class="sxs-lookup"><span data-stu-id="820d5-581">1163536 ms</span></span>         | <span data-ttu-id="820d5-582">596545536</span><span class="sxs-lookup"><span data-stu-id="820d5-582">596545536</span></span>                 | <span data-ttu-id="820d5-583">1273042毫秒</span><span class="sxs-lookup"><span data-stu-id="820d5-583">1273042 ms</span></span>         |
| <span data-ttu-id="820d5-584">**Entity Framework 6**</span><span class="sxs-lookup"><span data-stu-id="820d5-584">**Entity Framework 6**</span></span> | <span data-ttu-id="820d5-585">647127040</span><span class="sxs-lookup"><span data-stu-id="820d5-585">647127040</span></span>                 | <span data-ttu-id="820d5-586">190228毫秒</span><span class="sxs-lookup"><span data-stu-id="820d5-586">190228 ms</span></span>          | <span data-ttu-id="820d5-587">832798720</span><span class="sxs-lookup"><span data-stu-id="820d5-587">832798720</span></span>                 | <span data-ttu-id="820d5-588">195521毫秒</span><span class="sxs-lookup"><span data-stu-id="820d5-588">195521 ms</span></span>          |

<span data-ttu-id="820d5-589">Entity Framework 5 在執行結束時，會有較小的記憶體使用量，而不是 Entity Framework 6。</span><span class="sxs-lookup"><span data-stu-id="820d5-589">Entity Framework 5 will have a smaller memory footprint at the end of the run than Entity Framework 6 does.</span></span> <span data-ttu-id="820d5-590">Entity Framework 6 所耗用的額外記憶體，是提供新功能和更佳效能的額外記憶體結構和程式碼所造成的結果。</span><span class="sxs-lookup"><span data-stu-id="820d5-590">The additional memory consumed by Entity Framework 6 is the result of additional memory structures and code that enable new features and better performance.</span></span>

<span data-ttu-id="820d5-591">使用 ObjectStateManager 時，記憶體使用量也有清楚的差異。</span><span class="sxs-lookup"><span data-stu-id="820d5-591">There’s also a clear difference in memory footprint when using the ObjectStateManager.</span></span> <span data-ttu-id="820d5-592">Entity Framework 5 在追蹤我們從資料庫具體化的所有實體時，會增加30% 的使用量。</span><span class="sxs-lookup"><span data-stu-id="820d5-592">Entity Framework 5 increased its footprint by 30% when keeping track of all the entities we materialized from the database.</span></span> <span data-ttu-id="820d5-593">Entity Framework 6 這麼做時，增加了28% 的使用量。</span><span class="sxs-lookup"><span data-stu-id="820d5-593">Entity Framework 6 increased its footprint by 28% when doing so.</span></span>

<span data-ttu-id="820d5-594">在這段時間內，Entity Framework 6 在這項測試中的 Entity Framework 5 會以較大的邊界來勝過。</span><span class="sxs-lookup"><span data-stu-id="820d5-594">In terms of time, Entity Framework 6 outperforms Entity Framework 5 in this test by a large margin.</span></span> <span data-ttu-id="820d5-595">Entity Framework 6 已在 Entity Framework 5 所耗用的大約16% 的時間內完成測試。</span><span class="sxs-lookup"><span data-stu-id="820d5-595">Entity Framework 6 completed the test in roughly 16% of the time consumed by Entity Framework 5.</span></span> <span data-ttu-id="820d5-596">此外，當使用 ObjectStateManager 時，Entity Framework 5 會花費9% 以上的時間來完成。</span><span class="sxs-lookup"><span data-stu-id="820d5-596">Additionally, Entity Framework 5 takes 9% more time to complete when the ObjectStateManager is being used.</span></span> <span data-ttu-id="820d5-597">相較之下，使用 ObjectStateManager 時，Entity Framework 6 使用了3% 的時間。</span><span class="sxs-lookup"><span data-stu-id="820d5-597">In comparison, Entity Framework 6 is using 3% more time when using the ObjectStateManager.</span></span>

## <a name="6-query-execution-options"></a><span data-ttu-id="820d5-598">6查詢執行選項</span><span class="sxs-lookup"><span data-stu-id="820d5-598">6 Query Execution Options</span></span>

<span data-ttu-id="820d5-599">Entity Framework 提供數種不同的查詢方式。</span><span class="sxs-lookup"><span data-stu-id="820d5-599">Entity Framework offers several different ways to query.</span></span> <span data-ttu-id="820d5-600">我們將探討下列選項、比較各項的優缺點，並檢查其效能特性：</span><span class="sxs-lookup"><span data-stu-id="820d5-600">We'll take a look at the following options, compare the pros and cons of each, and examine their performance characteristics:</span></span>

-   <span data-ttu-id="820d5-601">LINQ to Entities。</span><span class="sxs-lookup"><span data-stu-id="820d5-601">LINQ to Entities.</span></span>
-   <span data-ttu-id="820d5-602">沒有追蹤 LINQ to Entities。</span><span class="sxs-lookup"><span data-stu-id="820d5-602">No Tracking LINQ to Entities.</span></span>
-   <span data-ttu-id="820d5-603">透過 ObjectQuery 的 Entity SQL。</span><span class="sxs-lookup"><span data-stu-id="820d5-603">Entity SQL over an ObjectQuery.</span></span>
-   <span data-ttu-id="820d5-604">透過 EntityCommand 的 Entity SQL。</span><span class="sxs-lookup"><span data-stu-id="820d5-604">Entity SQL over an EntityCommand.</span></span>
-   <span data-ttu-id="820d5-605">ExecuteStoreQuery.</span><span class="sxs-lookup"><span data-stu-id="820d5-605">ExecuteStoreQuery.</span></span>
-   <span data-ttu-id="820d5-606">SqlQuery.</span><span class="sxs-lookup"><span data-stu-id="820d5-606">SqlQuery.</span></span>
-   <span data-ttu-id="820d5-607">CompiledQuery.</span><span class="sxs-lookup"><span data-stu-id="820d5-607">CompiledQuery.</span></span>

### <a name="61-linq-to-entities-queries"></a><span data-ttu-id="820d5-608">6.1 LINQ to Entities 查詢</span><span class="sxs-lookup"><span data-stu-id="820d5-608">6.1       LINQ to Entities queries</span></span>

``` csharp
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
```

<span data-ttu-id="820d5-609">**展開**</span><span class="sxs-lookup"><span data-stu-id="820d5-609">**Pros**</span></span>

-   <span data-ttu-id="820d5-610">適用于 CUD 作業。</span><span class="sxs-lookup"><span data-stu-id="820d5-610">Suitable for CUD operations.</span></span>
-   <span data-ttu-id="820d5-611">完全具體化的物件。</span><span class="sxs-lookup"><span data-stu-id="820d5-611">Fully materialized objects.</span></span>
-   <span data-ttu-id="820d5-612">最簡單的方式是以程式設計語言內建的語法撰寫。</span><span class="sxs-lookup"><span data-stu-id="820d5-612">Simplest to write with syntax built into the programming language.</span></span>
-   <span data-ttu-id="820d5-613">良好的效能。</span><span class="sxs-lookup"><span data-stu-id="820d5-613">Good performance.</span></span>

<span data-ttu-id="820d5-614">**各有利弊**</span><span class="sxs-lookup"><span data-stu-id="820d5-614">**Cons**</span></span>

-   <span data-ttu-id="820d5-615">某些技術限制，例如：</span><span class="sxs-lookup"><span data-stu-id="820d5-615">Certain technical restrictions, such as:</span></span>
    -   <span data-ttu-id="820d5-616">使用 DefaultIfEmpty 進行外部聯結查詢的模式會導致比 Entity SQL 中的簡單外部聯結語句更複雜的查詢。</span><span class="sxs-lookup"><span data-stu-id="820d5-616">Patterns using DefaultIfEmpty for OUTER JOIN queries result in more complex queries than simple OUTER JOIN statements in Entity SQL.</span></span>
    -   <span data-ttu-id="820d5-617">您仍然無法搭配一般模式比對使用 LIKE。</span><span class="sxs-lookup"><span data-stu-id="820d5-617">You still can’t use LIKE with general pattern matching.</span></span>

### <a name="62-no-tracking-linq-to-entities-queries"></a><span data-ttu-id="820d5-618">6.2 沒有追蹤 LINQ to Entities 查詢</span><span class="sxs-lookup"><span data-stu-id="820d5-618">6.2       No Tracking LINQ to Entities queries</span></span>

<span data-ttu-id="820d5-619">當內容衍生 ObjectCoNtext 時：</span><span class="sxs-lookup"><span data-stu-id="820d5-619">When the context derives ObjectContext:</span></span>

``` csharp
context.Products.MergeOption = MergeOption.NoTracking;
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
```

<span data-ttu-id="820d5-620">當內容衍生 DbCoNtext 時：</span><span class="sxs-lookup"><span data-stu-id="820d5-620">When the context derives DbContext:</span></span>

``` csharp
var q = context.Products.AsNoTracking()
                        .Where(p => p.Category.CategoryName == "Beverages");
```

<span data-ttu-id="820d5-621">**展開**</span><span class="sxs-lookup"><span data-stu-id="820d5-621">**Pros**</span></span>

-   <span data-ttu-id="820d5-622">透過一般 LINQ 查詢改善效能。</span><span class="sxs-lookup"><span data-stu-id="820d5-622">Improved performance over regular LINQ queries.</span></span>
-   <span data-ttu-id="820d5-623">完全具體化的物件。</span><span class="sxs-lookup"><span data-stu-id="820d5-623">Fully materialized objects.</span></span>
-   <span data-ttu-id="820d5-624">最簡單的方式是以程式設計語言內建的語法撰寫。</span><span class="sxs-lookup"><span data-stu-id="820d5-624">Simplest to write with syntax built into the programming language.</span></span>

<span data-ttu-id="820d5-625">**各有利弊**</span><span class="sxs-lookup"><span data-stu-id="820d5-625">**Cons**</span></span>

-   <span data-ttu-id="820d5-626">不適用於 CUD 作業。</span><span class="sxs-lookup"><span data-stu-id="820d5-626">Not suitable for CUD operations.</span></span>
-   <span data-ttu-id="820d5-627">某些技術限制，例如：</span><span class="sxs-lookup"><span data-stu-id="820d5-627">Certain technical restrictions, such as:</span></span>
    -   <span data-ttu-id="820d5-628">使用 DefaultIfEmpty 進行外部聯結查詢的模式會導致比 Entity SQL 中的簡單外部聯結語句更複雜的查詢。</span><span class="sxs-lookup"><span data-stu-id="820d5-628">Patterns using DefaultIfEmpty for OUTER JOIN queries result in more complex queries than simple OUTER JOIN statements in Entity SQL.</span></span>
    -   <span data-ttu-id="820d5-629">您仍然無法搭配一般模式比對使用 LIKE。</span><span class="sxs-lookup"><span data-stu-id="820d5-629">You still can’t use LIKE with general pattern matching.</span></span>

<span data-ttu-id="820d5-630">請注意，即使未指定 NoTracking，也不會追蹤專案純量屬性的查詢。</span><span class="sxs-lookup"><span data-stu-id="820d5-630">Note that queries that project scalar properties are not tracked even if the NoTracking is not specified.</span></span> <span data-ttu-id="820d5-631">例如：</span><span class="sxs-lookup"><span data-stu-id="820d5-631">For example:</span></span>

``` csharp
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages").Select(p => new { p.ProductName });
```

<span data-ttu-id="820d5-632">此特定查詢並未明確指定為 NoTracking，但因為它不會具體化物件狀態管理員已知的類型，所以不會追蹤具體化結果。</span><span class="sxs-lookup"><span data-stu-id="820d5-632">This particular query doesn’t explicitly specify being NoTracking, but since it’s not materializing a type that’s known to the object state manager then the materialized result is not tracked.</span></span>

### <a name="63-entity-sql-over-an-objectquery"></a><span data-ttu-id="820d5-633">ObjectQuery 上的 6.3 Entity SQL</span><span class="sxs-lookup"><span data-stu-id="820d5-633">6.3       Entity SQL over an ObjectQuery</span></span>

``` csharp
ObjectQuery<Product> products = context.Products.Where("it.Category.CategoryName = 'Beverages'");
```

<span data-ttu-id="820d5-634">**展開**</span><span class="sxs-lookup"><span data-stu-id="820d5-634">**Pros**</span></span>

-   <span data-ttu-id="820d5-635">適用于 CUD 作業。</span><span class="sxs-lookup"><span data-stu-id="820d5-635">Suitable for CUD operations.</span></span>
-   <span data-ttu-id="820d5-636">完全具體化的物件。</span><span class="sxs-lookup"><span data-stu-id="820d5-636">Fully materialized objects.</span></span>
-   <span data-ttu-id="820d5-637">支援查詢計劃快取。</span><span class="sxs-lookup"><span data-stu-id="820d5-637">Supports query plan caching.</span></span>

<span data-ttu-id="820d5-638">**各有利弊**</span><span class="sxs-lookup"><span data-stu-id="820d5-638">**Cons**</span></span>

-   <span data-ttu-id="820d5-639">包含文字查詢字串，其比語言內建的查詢結構更容易發生使用者錯誤。</span><span class="sxs-lookup"><span data-stu-id="820d5-639">Involves textual query strings which are more prone to user error than query constructs built into the language.</span></span>

### <a name="64-entity-sql-over-an-entity-command"></a><span data-ttu-id="820d5-640">6.4 Entity SQL 透過實體命令</span><span class="sxs-lookup"><span data-stu-id="820d5-640">6.4       Entity SQL over an Entity Command</span></span>

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

<span data-ttu-id="820d5-641">**展開**</span><span class="sxs-lookup"><span data-stu-id="820d5-641">**Pros**</span></span>

-   <span data-ttu-id="820d5-642">支援 .NET 4.0 中的查詢計劃快取（.NET 4.5 中的所有其他查詢類型都支援計畫快取）。</span><span class="sxs-lookup"><span data-stu-id="820d5-642">Supports query plan caching in .NET 4.0 (plan caching is supported by all other query types in .NET 4.5).</span></span>

<span data-ttu-id="820d5-643">**各有利弊**</span><span class="sxs-lookup"><span data-stu-id="820d5-643">**Cons**</span></span>

-   <span data-ttu-id="820d5-644">包含文字查詢字串，其比語言內建的查詢結構更容易發生使用者錯誤。</span><span class="sxs-lookup"><span data-stu-id="820d5-644">Involves textual query strings which are more prone to user error than query constructs built into the language.</span></span>
-   <span data-ttu-id="820d5-645">不適用於 CUD 作業。</span><span class="sxs-lookup"><span data-stu-id="820d5-645">Not suitable for CUD operations.</span></span>
-   <span data-ttu-id="820d5-646">結果不會自動具體化，而且必須從資料讀取器讀取。</span><span class="sxs-lookup"><span data-stu-id="820d5-646">Results are not automatically materialized, and must be read from the data reader.</span></span>

### <a name="65-sqlquery-and-executestorequery"></a><span data-ttu-id="820d5-647">6.5 SqlQuery 和 ExecuteStoreQuery</span><span class="sxs-lookup"><span data-stu-id="820d5-647">6.5       SqlQuery and ExecuteStoreQuery</span></span>

<span data-ttu-id="820d5-648">資料庫上的 SqlQuery：</span><span class="sxs-lookup"><span data-stu-id="820d5-648">SqlQuery on Database:</span></span>

``` csharp
// use this to obtain entities and not track them
var q1 = context.Database.SqlQuery<Product>("select * from products");
```

<span data-ttu-id="820d5-649">DbSet 上的 SqlQuery：</span><span class="sxs-lookup"><span data-stu-id="820d5-649">SqlQuery on DbSet:</span></span>

``` csharp
// use this to obtain entities and have them tracked
var q2 = context.Products.SqlQuery("select * from products");
```

<span data-ttu-id="820d5-650">ExecyteStoreQuery:</span><span class="sxs-lookup"><span data-stu-id="820d5-650">ExecyteStoreQuery:</span></span>

``` csharp
var beverages = context.ExecuteStoreQuery<Product>(
@"     SELECT        P.ProductID, P.ProductName, P.SupplierID, P.CategoryID, P.QuantityPerUnit, P.UnitPrice, P.UnitsInStock, P.UnitsOnOrder, P.ReorderLevel, P.Discontinued, P.DiscontinuedDate
       FROM            Products AS P INNER JOIN Categories AS C ON P.CategoryID = C.CategoryID
       WHERE        (C.CategoryName = 'Beverages')"
);
```

<span data-ttu-id="820d5-651">**展開**</span><span class="sxs-lookup"><span data-stu-id="820d5-651">**Pros**</span></span>

-   <span data-ttu-id="820d5-652">因為已略過計畫編譯器，所以通常是最快的效能。</span><span class="sxs-lookup"><span data-stu-id="820d5-652">Generally fastest performance since plan compiler is bypassed.</span></span>
-   <span data-ttu-id="820d5-653">完全具體化的物件。</span><span class="sxs-lookup"><span data-stu-id="820d5-653">Fully materialized objects.</span></span>
-   <span data-ttu-id="820d5-654">適用于從 DbSet 使用時的 CUD 作業。</span><span class="sxs-lookup"><span data-stu-id="820d5-654">Suitable for CUD operations when used from the DbSet.</span></span>

<span data-ttu-id="820d5-655">**各有利弊**</span><span class="sxs-lookup"><span data-stu-id="820d5-655">**Cons**</span></span>

-   <span data-ttu-id="820d5-656">查詢是文字且容易出錯。</span><span class="sxs-lookup"><span data-stu-id="820d5-656">Query is textual and error prone.</span></span>
-   <span data-ttu-id="820d5-657">查詢系結至特定後端，方法是使用存放區的語義，而不是概念性的語義。</span><span class="sxs-lookup"><span data-stu-id="820d5-657">Query is tied to a specific backend by using store semantics instead of conceptual semantics.</span></span>
-   <span data-ttu-id="820d5-658">當繼承存在時，手工打造 query 必須考慮所要求之類型的對應條件。</span><span class="sxs-lookup"><span data-stu-id="820d5-658">When inheritance is present, handcrafted query needs to account for mapping conditions for the type requested.</span></span>

### <a name="66-compiledquery"></a><span data-ttu-id="820d5-659">6.6 CompiledQuery</span><span class="sxs-lookup"><span data-stu-id="820d5-659">6.6       CompiledQuery</span></span>

``` csharp
private static readonly Func<NorthwindEntities, string, IQueryable<Product>> productsForCategoryCQ = CompiledQuery.Compile(
    (NorthwindEntities context, string categoryName) =>
        context.Products.Where(p => p.Category.CategoryName == categoryName)
        );
…
var q = context.InvokeProductsForCategoryCQ("Beverages");
```

<span data-ttu-id="820d5-660">**展開**</span><span class="sxs-lookup"><span data-stu-id="820d5-660">**Pros**</span></span>

-   <span data-ttu-id="820d5-661">透過一般 LINQ 查詢，提供最高7% 的效能改進。</span><span class="sxs-lookup"><span data-stu-id="820d5-661">Provides up to a 7% performance improvement over regular LINQ queries.</span></span>
-   <span data-ttu-id="820d5-662">完全具體化的物件。</span><span class="sxs-lookup"><span data-stu-id="820d5-662">Fully materialized objects.</span></span>
-   <span data-ttu-id="820d5-663">適用于 CUD 作業。</span><span class="sxs-lookup"><span data-stu-id="820d5-663">Suitable for CUD operations.</span></span>

<span data-ttu-id="820d5-664">**各有利弊**</span><span class="sxs-lookup"><span data-stu-id="820d5-664">**Cons**</span></span>

-   <span data-ttu-id="820d5-665">增加複雜度和程式設計負荷。</span><span class="sxs-lookup"><span data-stu-id="820d5-665">Increased complexity and programming overhead.</span></span>
-   <span data-ttu-id="820d5-666">在編譯的查詢之上撰寫時，效能改進會遺失。</span><span class="sxs-lookup"><span data-stu-id="820d5-666">The performance improvement is lost when composing on top of a compiled query.</span></span>
-   <span data-ttu-id="820d5-667">有些 LINQ 查詢無法以 CompiledQuery 的形式寫入，例如匿名型別的預測。</span><span class="sxs-lookup"><span data-stu-id="820d5-667">Some LINQ queries can't be written as a CompiledQuery - for example, projections of anonymous types.</span></span>

### <a name="67-performance-comparison-of-different-query-options"></a><span data-ttu-id="820d5-668">6.7 不同查詢選項的效能比較</span><span class="sxs-lookup"><span data-stu-id="820d5-668">6.7       Performance Comparison of different query options</span></span>

<span data-ttu-id="820d5-669">簡單的 microbenchmarks，其中的內容建立並未計時，會放入測試中。</span><span class="sxs-lookup"><span data-stu-id="820d5-669">Simple microbenchmarks where the context creation was not timed were put to the test.</span></span> <span data-ttu-id="820d5-670">我們針對受控制環境中的一組非快取實體，測量了5000次的查詢。</span><span class="sxs-lookup"><span data-stu-id="820d5-670">We measured querying 5000 times for a set of non-cached entities in a controlled environment.</span></span> <span data-ttu-id="820d5-671">這些數位是以警告來取得：它們不會反映應用程式所產生的實際數目，而是在比較不同的查詢選項時，會非常精確地測量效能差異的程度。蘋果到蘋果，不包括建立新內容的成本。</span><span class="sxs-lookup"><span data-stu-id="820d5-671">These numbers are to be taken with a warning: they do not reflect actual numbers produced by an application, but instead they are a very accurate measurement of how much of a performance difference there is when different querying options are compared apples-to-apples, excluding the cost of creating a new context.</span></span>

| <span data-ttu-id="820d5-672">EF</span><span class="sxs-lookup"><span data-stu-id="820d5-672">EF</span></span>  | <span data-ttu-id="820d5-673">測試</span><span class="sxs-lookup"><span data-stu-id="820d5-673">Test</span></span>                                 | <span data-ttu-id="820d5-674">時間（毫秒）</span><span class="sxs-lookup"><span data-stu-id="820d5-674">Time (ms)</span></span> | <span data-ttu-id="820d5-675">記憶體</span><span class="sxs-lookup"><span data-stu-id="820d5-675">Memory</span></span>   |
|:----|:-------------------------------------|:----------|:---------|
| <span data-ttu-id="820d5-676">EF5</span><span class="sxs-lookup"><span data-stu-id="820d5-676">EF5</span></span> | <span data-ttu-id="820d5-677">ObjectCoNtext ESQL</span><span class="sxs-lookup"><span data-stu-id="820d5-677">ObjectContext ESQL</span></span>                   | <span data-ttu-id="820d5-678">2414</span><span class="sxs-lookup"><span data-stu-id="820d5-678">2414</span></span>      | <span data-ttu-id="820d5-679">38801408</span><span class="sxs-lookup"><span data-stu-id="820d5-679">38801408</span></span> |
| <span data-ttu-id="820d5-680">EF5</span><span class="sxs-lookup"><span data-stu-id="820d5-680">EF5</span></span> | <span data-ttu-id="820d5-681">ObjectCoNtext Linq 查詢</span><span class="sxs-lookup"><span data-stu-id="820d5-681">ObjectContext Linq Query</span></span>             | <span data-ttu-id="820d5-682">2692</span><span class="sxs-lookup"><span data-stu-id="820d5-682">2692</span></span>      | <span data-ttu-id="820d5-683">38277120</span><span class="sxs-lookup"><span data-stu-id="820d5-683">38277120</span></span> |
| <span data-ttu-id="820d5-684">EF5</span><span class="sxs-lookup"><span data-stu-id="820d5-684">EF5</span></span> | <span data-ttu-id="820d5-685">DbCoNtext Linq 查詢沒有追蹤</span><span class="sxs-lookup"><span data-stu-id="820d5-685">DbContext Linq Query No Tracking</span></span>     | <span data-ttu-id="820d5-686">2818</span><span class="sxs-lookup"><span data-stu-id="820d5-686">2818</span></span>      | <span data-ttu-id="820d5-687">41840640</span><span class="sxs-lookup"><span data-stu-id="820d5-687">41840640</span></span> |
| <span data-ttu-id="820d5-688">EF5</span><span class="sxs-lookup"><span data-stu-id="820d5-688">EF5</span></span> | <span data-ttu-id="820d5-689">DbCoNtext Linq 查詢</span><span class="sxs-lookup"><span data-stu-id="820d5-689">DbContext Linq Query</span></span>                 | <span data-ttu-id="820d5-690">2930</span><span class="sxs-lookup"><span data-stu-id="820d5-690">2930</span></span>      | <span data-ttu-id="820d5-691">41771008</span><span class="sxs-lookup"><span data-stu-id="820d5-691">41771008</span></span> |
| <span data-ttu-id="820d5-692">EF5</span><span class="sxs-lookup"><span data-stu-id="820d5-692">EF5</span></span> | <span data-ttu-id="820d5-693">ObjectCoNtext Linq 查詢沒有追蹤</span><span class="sxs-lookup"><span data-stu-id="820d5-693">ObjectContext Linq Query No Tracking</span></span> | <span data-ttu-id="820d5-694">3013</span><span class="sxs-lookup"><span data-stu-id="820d5-694">3013</span></span>      | <span data-ttu-id="820d5-695">38412288</span><span class="sxs-lookup"><span data-stu-id="820d5-695">38412288</span></span> |
|     |                                      |           |          |
| <span data-ttu-id="820d5-696">EF6</span><span class="sxs-lookup"><span data-stu-id="820d5-696">EF6</span></span> | <span data-ttu-id="820d5-697">ObjectCoNtext ESQL</span><span class="sxs-lookup"><span data-stu-id="820d5-697">ObjectContext ESQL</span></span>                   | <span data-ttu-id="820d5-698">2059</span><span class="sxs-lookup"><span data-stu-id="820d5-698">2059</span></span>      | <span data-ttu-id="820d5-699">46039040</span><span class="sxs-lookup"><span data-stu-id="820d5-699">46039040</span></span> |
| <span data-ttu-id="820d5-700">EF6</span><span class="sxs-lookup"><span data-stu-id="820d5-700">EF6</span></span> | <span data-ttu-id="820d5-701">ObjectCoNtext Linq 查詢</span><span class="sxs-lookup"><span data-stu-id="820d5-701">ObjectContext Linq Query</span></span>             | <span data-ttu-id="820d5-702">3074</span><span class="sxs-lookup"><span data-stu-id="820d5-702">3074</span></span>      | <span data-ttu-id="820d5-703">45248512</span><span class="sxs-lookup"><span data-stu-id="820d5-703">45248512</span></span> |
| <span data-ttu-id="820d5-704">EF6</span><span class="sxs-lookup"><span data-stu-id="820d5-704">EF6</span></span> | <span data-ttu-id="820d5-705">DbCoNtext Linq 查詢沒有追蹤</span><span class="sxs-lookup"><span data-stu-id="820d5-705">DbContext Linq Query No Tracking</span></span>     | <span data-ttu-id="820d5-706">3125</span><span class="sxs-lookup"><span data-stu-id="820d5-706">3125</span></span>      | <span data-ttu-id="820d5-707">47575040</span><span class="sxs-lookup"><span data-stu-id="820d5-707">47575040</span></span> |
| <span data-ttu-id="820d5-708">EF6</span><span class="sxs-lookup"><span data-stu-id="820d5-708">EF6</span></span> | <span data-ttu-id="820d5-709">DbCoNtext Linq 查詢</span><span class="sxs-lookup"><span data-stu-id="820d5-709">DbContext Linq Query</span></span>                 | <span data-ttu-id="820d5-710">3420</span><span class="sxs-lookup"><span data-stu-id="820d5-710">3420</span></span>      | <span data-ttu-id="820d5-711">47652864</span><span class="sxs-lookup"><span data-stu-id="820d5-711">47652864</span></span> |
| <span data-ttu-id="820d5-712">EF6</span><span class="sxs-lookup"><span data-stu-id="820d5-712">EF6</span></span> | <span data-ttu-id="820d5-713">ObjectCoNtext Linq 查詢沒有追蹤</span><span class="sxs-lookup"><span data-stu-id="820d5-713">ObjectContext Linq Query No Tracking</span></span> | <span data-ttu-id="820d5-714">3593</span><span class="sxs-lookup"><span data-stu-id="820d5-714">3593</span></span>      | <span data-ttu-id="820d5-715">45260800</span><span class="sxs-lookup"><span data-stu-id="820d5-715">45260800</span></span> |

![EF5 微基準測試，5000暖反復專案](~/ef6/media/ef5micro5000warm.png)

![EF6 微基準測試，5000暖反復專案](~/ef6/media/ef6micro5000warm.png)

<span data-ttu-id="820d5-718">Microbenchmarks 對程式碼中的小型變更非常敏感。</span><span class="sxs-lookup"><span data-stu-id="820d5-718">Microbenchmarks are very sensitive to small changes in the code.</span></span> <span data-ttu-id="820d5-719">在此情況下，Entity Framework 5 和 Entity Framework 6 的成本差異，是因為新增了[攔截](~/ef6/fundamentals/logging-and-interception.md)和交易的改良[功能](~/ef6/saving/transactions.md)。</span><span class="sxs-lookup"><span data-stu-id="820d5-719">In this case, the difference between the costs of Entity Framework 5 and Entity Framework 6 are due to the addition of [interception](~/ef6/fundamentals/logging-and-interception.md) and [transactional improvements](~/ef6/saving/transactions.md).</span></span> <span data-ttu-id="820d5-720">不過，這些 microbenchmarks 的數位是 Entity Framework 的一小部分，</span><span class="sxs-lookup"><span data-stu-id="820d5-720">These microbenchmarks numbers, however, are an amplified vision into a very small fragment of what Entity Framework does.</span></span> <span data-ttu-id="820d5-721">從 Entity Framework 5 升級至 Entity Framework 6 時，經常性查詢的實際案例應該不會看到效能衰退。</span><span class="sxs-lookup"><span data-stu-id="820d5-721">Real-world scenarios of warm queries should not see a performance regression when upgrading from Entity Framework 5 to Entity Framework 6.</span></span>

<span data-ttu-id="820d5-722">為了比較不同查詢選項的實際效能，我們建立了5個不同的測試變化，讓我們使用不同的查詢選項來選取類別目錄名稱為「飲料」的所有產品。</span><span class="sxs-lookup"><span data-stu-id="820d5-722">To compare the real-world performance of the different query options, we created 5 separate test variations where we use a different query option to select all products whose category name is "Beverages".</span></span> <span data-ttu-id="820d5-723">每個反復專案都包含建立內容的成本，以及將所有傳回的實體具體化的成本。</span><span class="sxs-lookup"><span data-stu-id="820d5-723">Each iteration includes the cost of creating the context, and the cost of materializing all returned entities.</span></span> <span data-ttu-id="820d5-724">在取得1000計時反復專案總和之前，會執行10次反覆運算 untimed。</span><span class="sxs-lookup"><span data-stu-id="820d5-724">10 iterations are run untimed before taking the sum of 1000 timed iterations.</span></span> <span data-ttu-id="820d5-725">顯示的結果是從每個測試的5個回合中執行的中間值。</span><span class="sxs-lookup"><span data-stu-id="820d5-725">The results shown are the median run taken from 5 runs of each test.</span></span> <span data-ttu-id="820d5-726">如需詳細資訊，請參閱附錄 B，其中包含測試的程式碼。</span><span class="sxs-lookup"><span data-stu-id="820d5-726">For more information, see Appendix B which includes the code for the test.</span></span>

| <span data-ttu-id="820d5-727">EF</span><span class="sxs-lookup"><span data-stu-id="820d5-727">EF</span></span>  | <span data-ttu-id="820d5-728">測試</span><span class="sxs-lookup"><span data-stu-id="820d5-728">Test</span></span>                                        | <span data-ttu-id="820d5-729">時間（毫秒）</span><span class="sxs-lookup"><span data-stu-id="820d5-729">Time (ms)</span></span> | <span data-ttu-id="820d5-730">記憶體</span><span class="sxs-lookup"><span data-stu-id="820d5-730">Memory</span></span>   |
|:----|:--------------------------------------------|:----------|:---------|
| <span data-ttu-id="820d5-731">EF5</span><span class="sxs-lookup"><span data-stu-id="820d5-731">EF5</span></span> | <span data-ttu-id="820d5-732">ObjectCoNtext 實體命令</span><span class="sxs-lookup"><span data-stu-id="820d5-732">ObjectContext Entity Command</span></span>                | <span data-ttu-id="820d5-733">621</span><span class="sxs-lookup"><span data-stu-id="820d5-733">621</span></span>       | <span data-ttu-id="820d5-734">39350272</span><span class="sxs-lookup"><span data-stu-id="820d5-734">39350272</span></span> |
| <span data-ttu-id="820d5-735">EF5</span><span class="sxs-lookup"><span data-stu-id="820d5-735">EF5</span></span> | <span data-ttu-id="820d5-736">在資料庫上 DbCoNtext Sql 查詢</span><span class="sxs-lookup"><span data-stu-id="820d5-736">DbContext Sql Query on Database</span></span>             | <span data-ttu-id="820d5-737">825</span><span class="sxs-lookup"><span data-stu-id="820d5-737">825</span></span>       | <span data-ttu-id="820d5-738">37519360</span><span class="sxs-lookup"><span data-stu-id="820d5-738">37519360</span></span> |
| <span data-ttu-id="820d5-739">EF5</span><span class="sxs-lookup"><span data-stu-id="820d5-739">EF5</span></span> | <span data-ttu-id="820d5-740">ObjectCoNtext 存放區查詢</span><span class="sxs-lookup"><span data-stu-id="820d5-740">ObjectContext Store Query</span></span>                   | <span data-ttu-id="820d5-741">878</span><span class="sxs-lookup"><span data-stu-id="820d5-741">878</span></span>       | <span data-ttu-id="820d5-742">39460864</span><span class="sxs-lookup"><span data-stu-id="820d5-742">39460864</span></span> |
| <span data-ttu-id="820d5-743">EF5</span><span class="sxs-lookup"><span data-stu-id="820d5-743">EF5</span></span> | <span data-ttu-id="820d5-744">ObjectCoNtext Linq 查詢沒有追蹤</span><span class="sxs-lookup"><span data-stu-id="820d5-744">ObjectContext Linq Query No Tracking</span></span>        | <span data-ttu-id="820d5-745">969</span><span class="sxs-lookup"><span data-stu-id="820d5-745">969</span></span>       | <span data-ttu-id="820d5-746">38293504</span><span class="sxs-lookup"><span data-stu-id="820d5-746">38293504</span></span> |
| <span data-ttu-id="820d5-747">EF5</span><span class="sxs-lookup"><span data-stu-id="820d5-747">EF5</span></span> | <span data-ttu-id="820d5-748">使用物件查詢的 ObjectCoNtext Entity Sql</span><span class="sxs-lookup"><span data-stu-id="820d5-748">ObjectContext Entity Sql using Object Query</span></span> | <span data-ttu-id="820d5-749">1089</span><span class="sxs-lookup"><span data-stu-id="820d5-749">1089</span></span>      | <span data-ttu-id="820d5-750">38981632</span><span class="sxs-lookup"><span data-stu-id="820d5-750">38981632</span></span> |
| <span data-ttu-id="820d5-751">EF5</span><span class="sxs-lookup"><span data-stu-id="820d5-751">EF5</span></span> | <span data-ttu-id="820d5-752">ObjectCoNtext 已編譯查詢</span><span class="sxs-lookup"><span data-stu-id="820d5-752">ObjectContext Compiled Query</span></span>                | <span data-ttu-id="820d5-753">1099</span><span class="sxs-lookup"><span data-stu-id="820d5-753">1099</span></span>      | <span data-ttu-id="820d5-754">38682624</span><span class="sxs-lookup"><span data-stu-id="820d5-754">38682624</span></span> |
| <span data-ttu-id="820d5-755">EF5</span><span class="sxs-lookup"><span data-stu-id="820d5-755">EF5</span></span> | <span data-ttu-id="820d5-756">ObjectCoNtext Linq 查詢</span><span class="sxs-lookup"><span data-stu-id="820d5-756">ObjectContext Linq Query</span></span>                    | <span data-ttu-id="820d5-757">1152</span><span class="sxs-lookup"><span data-stu-id="820d5-757">1152</span></span>      | <span data-ttu-id="820d5-758">38178816</span><span class="sxs-lookup"><span data-stu-id="820d5-758">38178816</span></span> |
| <span data-ttu-id="820d5-759">EF5</span><span class="sxs-lookup"><span data-stu-id="820d5-759">EF5</span></span> | <span data-ttu-id="820d5-760">DbCoNtext Linq 查詢沒有追蹤</span><span class="sxs-lookup"><span data-stu-id="820d5-760">DbContext Linq Query No Tracking</span></span>            | <span data-ttu-id="820d5-761">1208</span><span class="sxs-lookup"><span data-stu-id="820d5-761">1208</span></span>      | <span data-ttu-id="820d5-762">41803776</span><span class="sxs-lookup"><span data-stu-id="820d5-762">41803776</span></span> |
| <span data-ttu-id="820d5-763">EF5</span><span class="sxs-lookup"><span data-stu-id="820d5-763">EF5</span></span> | <span data-ttu-id="820d5-764">DbSet 上的 DbCoNtext Sql 查詢</span><span class="sxs-lookup"><span data-stu-id="820d5-764">DbContext Sql Query on DbSet</span></span>                | <span data-ttu-id="820d5-765">1414</span><span class="sxs-lookup"><span data-stu-id="820d5-765">1414</span></span>      | <span data-ttu-id="820d5-766">37982208</span><span class="sxs-lookup"><span data-stu-id="820d5-766">37982208</span></span> |
| <span data-ttu-id="820d5-767">EF5</span><span class="sxs-lookup"><span data-stu-id="820d5-767">EF5</span></span> | <span data-ttu-id="820d5-768">DbCoNtext Linq 查詢</span><span class="sxs-lookup"><span data-stu-id="820d5-768">DbContext Linq Query</span></span>                        | <span data-ttu-id="820d5-769">1574</span><span class="sxs-lookup"><span data-stu-id="820d5-769">1574</span></span>      | <span data-ttu-id="820d5-770">41738240</span><span class="sxs-lookup"><span data-stu-id="820d5-770">41738240</span></span> |
|     |                                             |           |          |
| <span data-ttu-id="820d5-771">EF6</span><span class="sxs-lookup"><span data-stu-id="820d5-771">EF6</span></span> | <span data-ttu-id="820d5-772">ObjectCoNtext 實體命令</span><span class="sxs-lookup"><span data-stu-id="820d5-772">ObjectContext Entity Command</span></span>                | <span data-ttu-id="820d5-773">480</span><span class="sxs-lookup"><span data-stu-id="820d5-773">480</span></span>       | <span data-ttu-id="820d5-774">47247360</span><span class="sxs-lookup"><span data-stu-id="820d5-774">47247360</span></span> |
| <span data-ttu-id="820d5-775">EF6</span><span class="sxs-lookup"><span data-stu-id="820d5-775">EF6</span></span> | <span data-ttu-id="820d5-776">ObjectCoNtext 存放區查詢</span><span class="sxs-lookup"><span data-stu-id="820d5-776">ObjectContext Store Query</span></span>                   | <span data-ttu-id="820d5-777">493</span><span class="sxs-lookup"><span data-stu-id="820d5-777">493</span></span>       | <span data-ttu-id="820d5-778">46739456</span><span class="sxs-lookup"><span data-stu-id="820d5-778">46739456</span></span> |
| <span data-ttu-id="820d5-779">EF6</span><span class="sxs-lookup"><span data-stu-id="820d5-779">EF6</span></span> | <span data-ttu-id="820d5-780">在資料庫上 DbCoNtext Sql 查詢</span><span class="sxs-lookup"><span data-stu-id="820d5-780">DbContext Sql Query on Database</span></span>             | <span data-ttu-id="820d5-781">614</span><span class="sxs-lookup"><span data-stu-id="820d5-781">614</span></span>       | <span data-ttu-id="820d5-782">41607168</span><span class="sxs-lookup"><span data-stu-id="820d5-782">41607168</span></span> |
| <span data-ttu-id="820d5-783">EF6</span><span class="sxs-lookup"><span data-stu-id="820d5-783">EF6</span></span> | <span data-ttu-id="820d5-784">ObjectCoNtext Linq 查詢沒有追蹤</span><span class="sxs-lookup"><span data-stu-id="820d5-784">ObjectContext Linq Query No Tracking</span></span>        | <span data-ttu-id="820d5-785">684</span><span class="sxs-lookup"><span data-stu-id="820d5-785">684</span></span>       | <span data-ttu-id="820d5-786">46333952</span><span class="sxs-lookup"><span data-stu-id="820d5-786">46333952</span></span> |
| <span data-ttu-id="820d5-787">EF6</span><span class="sxs-lookup"><span data-stu-id="820d5-787">EF6</span></span> | <span data-ttu-id="820d5-788">使用物件查詢的 ObjectCoNtext Entity Sql</span><span class="sxs-lookup"><span data-stu-id="820d5-788">ObjectContext Entity Sql using Object Query</span></span> | <span data-ttu-id="820d5-789">767</span><span class="sxs-lookup"><span data-stu-id="820d5-789">767</span></span>       | <span data-ttu-id="820d5-790">48865280</span><span class="sxs-lookup"><span data-stu-id="820d5-790">48865280</span></span> |
| <span data-ttu-id="820d5-791">EF6</span><span class="sxs-lookup"><span data-stu-id="820d5-791">EF6</span></span> | <span data-ttu-id="820d5-792">ObjectCoNtext 已編譯查詢</span><span class="sxs-lookup"><span data-stu-id="820d5-792">ObjectContext Compiled Query</span></span>                | <span data-ttu-id="820d5-793">788</span><span class="sxs-lookup"><span data-stu-id="820d5-793">788</span></span>       | <span data-ttu-id="820d5-794">48467968</span><span class="sxs-lookup"><span data-stu-id="820d5-794">48467968</span></span> |
| <span data-ttu-id="820d5-795">EF6</span><span class="sxs-lookup"><span data-stu-id="820d5-795">EF6</span></span> | <span data-ttu-id="820d5-796">DbCoNtext Linq 查詢沒有追蹤</span><span class="sxs-lookup"><span data-stu-id="820d5-796">DbContext Linq Query No Tracking</span></span>            | <span data-ttu-id="820d5-797">878</span><span class="sxs-lookup"><span data-stu-id="820d5-797">878</span></span>       | <span data-ttu-id="820d5-798">47554560</span><span class="sxs-lookup"><span data-stu-id="820d5-798">47554560</span></span> |
| <span data-ttu-id="820d5-799">EF6</span><span class="sxs-lookup"><span data-stu-id="820d5-799">EF6</span></span> | <span data-ttu-id="820d5-800">ObjectCoNtext Linq 查詢</span><span class="sxs-lookup"><span data-stu-id="820d5-800">ObjectContext Linq Query</span></span>                    | <span data-ttu-id="820d5-801">953</span><span class="sxs-lookup"><span data-stu-id="820d5-801">953</span></span>       | <span data-ttu-id="820d5-802">47632384</span><span class="sxs-lookup"><span data-stu-id="820d5-802">47632384</span></span> |
| <span data-ttu-id="820d5-803">EF6</span><span class="sxs-lookup"><span data-stu-id="820d5-803">EF6</span></span> | <span data-ttu-id="820d5-804">DbSet 上的 DbCoNtext Sql 查詢</span><span class="sxs-lookup"><span data-stu-id="820d5-804">DbContext Sql Query on DbSet</span></span>                | <span data-ttu-id="820d5-805">1023</span><span class="sxs-lookup"><span data-stu-id="820d5-805">1023</span></span>      | <span data-ttu-id="820d5-806">41992192</span><span class="sxs-lookup"><span data-stu-id="820d5-806">41992192</span></span> |
| <span data-ttu-id="820d5-807">EF6</span><span class="sxs-lookup"><span data-stu-id="820d5-807">EF6</span></span> | <span data-ttu-id="820d5-808">DbCoNtext Linq 查詢</span><span class="sxs-lookup"><span data-stu-id="820d5-808">DbContext Linq Query</span></span>                        | <span data-ttu-id="820d5-809">1290</span><span class="sxs-lookup"><span data-stu-id="820d5-809">1290</span></span>      | <span data-ttu-id="820d5-810">47529984</span><span class="sxs-lookup"><span data-stu-id="820d5-810">47529984</span></span> |


![EF5 暖查詢1000反覆運算](~/ef6/media/ef5warmquery1000.png)

![EF6 暖查詢1000反覆運算](~/ef6/media/ef6warmquery1000.png)

> [!NOTE]
> <span data-ttu-id="820d5-813">為求完整性，我們包含在 EntityCommand 上執行 Entity SQL 查詢的變化。</span><span class="sxs-lookup"><span data-stu-id="820d5-813">For completeness, we included a variation where we execute an Entity SQL query on an EntityCommand.</span></span> <span data-ttu-id="820d5-814">不過，因為這類查詢不會具體化結果，所以比較不一定是蘋果對蘋果。</span><span class="sxs-lookup"><span data-stu-id="820d5-814">However, because results are not materialized for such queries, the comparison isn't necessarily apples-to-apples.</span></span> <span data-ttu-id="820d5-815">測試包括要具體化的收盤近似值，以嘗試進行比較 fairer。</span><span class="sxs-lookup"><span data-stu-id="820d5-815">The test includes a close approximation to materializing to try making the comparison fairer.</span></span>

<span data-ttu-id="820d5-816">在此端對端案例中，由於在堆疊的數個部分進行效能改進，Entity Framework 6 勝過 Entity Framework 5，包括更輕量的 DbCoNtext 初始化，以及更快的 MetadataCollection&lt;T&gt; 查閱。</span><span class="sxs-lookup"><span data-stu-id="820d5-816">In this end-to-end case, Entity Framework 6 outperforms Entity Framework 5 due to performance improvements made on several parts of the stack, including a much lighter DbContext initialization and faster MetadataCollection&lt;T&gt; lookups.</span></span>

## <a name="7-design-time-performance-considerations"></a><span data-ttu-id="820d5-817">7設計階段效能考慮</span><span class="sxs-lookup"><span data-stu-id="820d5-817">7 Design time performance considerations</span></span>

### <a name="71-inheritance-strategies"></a><span data-ttu-id="820d5-818">7.1 繼承策略</span><span class="sxs-lookup"><span data-stu-id="820d5-818">7.1       Inheritance Strategies</span></span>

<span data-ttu-id="820d5-819">使用 Entity Framework 時的另一個效能考慮是您所使用的繼承策略。</span><span class="sxs-lookup"><span data-stu-id="820d5-819">Another performance consideration when using Entity Framework is the inheritance strategy you use.</span></span> <span data-ttu-id="820d5-820">Entity Framework 支援3種基本類型的繼承和其組合：</span><span class="sxs-lookup"><span data-stu-id="820d5-820">Entity Framework supports 3 basic types of inheritance and their combinations:</span></span>

-   <span data-ttu-id="820d5-821">每個階層的資料表（TPH）–其中每個繼承集都對應到具有鑒別子資料行的資料表，以指出階層中的哪一個特定類型要在資料列中表示。</span><span class="sxs-lookup"><span data-stu-id="820d5-821">Table per Hierarchy (TPH) – where each inheritance set maps to a table with a discriminator column to indicate which particular type in the hierarchy is being represented in the row.</span></span>
-   <span data-ttu-id="820d5-822">每個類型的資料表（TPT）–其中每個類型在資料庫中都有自己的資料表;子資料工作表只會定義父資料表不包含的資料行。</span><span class="sxs-lookup"><span data-stu-id="820d5-822">Table per Type (TPT) – where each type has its own table in the database; the child tables only define the columns that the parent table doesn’t contain.</span></span>
-   <span data-ttu-id="820d5-823">每個類別的資料表（TPC）–其中每個類型在資料庫中都有自己的完整資料表。子資料工作表會定義其所有欄位，包括在父類型中定義的欄位。</span><span class="sxs-lookup"><span data-stu-id="820d5-823">Table per Class (TPC) – where each type has its own full table in the database; the child tables define all their fields, including those defined in parent types.</span></span>

<span data-ttu-id="820d5-824">如果您的模型使用 TPT 繼承，則產生的查詢會比使用其他繼承策略產生的查詢更為複雜，這可能會導致存放區上的執行時間較長。</span><span class="sxs-lookup"><span data-stu-id="820d5-824">If your model uses TPT inheritance, the queries which are generated will be more complex than those that are generated with the other inheritance strategies, which may result on longer execution times on the store.</span></span><span data-ttu-id="820d5-825">  通常會花較長的時間來產生 TPT 模型的查詢，並具體化產生的物件。</span><span class="sxs-lookup"><span data-stu-id="820d5-825">  It will generally take longer to generate queries over a TPT model, and to materialize the resulting objects.</span></span>

<span data-ttu-id="820d5-826">請參閱 Entity Framework 的 MSDN blog 文章： \<http://blogs.msdn.com/b/adonet/archive/2010/08/17/performance-considerations-when-using-tpt-table-per-type-inheritance-in-the-entity-framework.aspx>中的 使用 TPT （每一類型的資料表）繼承的效能考慮。</span><span class="sxs-lookup"><span data-stu-id="820d5-826">See the "Performance Considerations when using TPT (Table per Type) Inheritance in the Entity Framework" MSDN blog post: \<http://blogs.msdn.com/b/adonet/archive/2010/08/17/performance-considerations-when-using-tpt-table-per-type-inheritance-in-the-entity-framework.aspx>.</span></span>

#### <a name="711-avoiding-tpt-in-model-first-or-code-first-applications"></a><span data-ttu-id="820d5-827">7.1.1 避免在 Model First 或 Code First 應用程式中進行 TPT</span><span class="sxs-lookup"><span data-stu-id="820d5-827">7.1.1       Avoiding TPT in Model First or Code First applications</span></span>

<span data-ttu-id="820d5-828">當您透過具有 TPT 架構的現有資料庫建立模型時，您不會有許多選項。</span><span class="sxs-lookup"><span data-stu-id="820d5-828">When you create a model over an existing database that has a TPT schema, you don't have many options.</span></span> <span data-ttu-id="820d5-829">但是，使用 Model First 或 Code First 建立應用程式時，您應該避免 TPT 繼承以進行效能考慮。</span><span class="sxs-lookup"><span data-stu-id="820d5-829">But when creating an application using Model First or Code First, you should avoid TPT inheritance for performance concerns.</span></span>

<span data-ttu-id="820d5-830">當您使用 Entity Designer Wizard 中的 Model First 時，您會取得模型中任何繼承的 TPT。</span><span class="sxs-lookup"><span data-stu-id="820d5-830">When you use Model First in the Entity Designer Wizard, you will get TPT for any inheritance in your model.</span></span> <span data-ttu-id="820d5-831">如果您想要使用 Model First 切換到 TPH 繼承策略，可以使用 Visual Studio 資源庫（\<http://visualstudiogallery.msdn.microsoft.com/df3541c3-d833-4b65-b942-989e7ec74c87/>)中的「Entity Designer 資料庫產生功能」。</span><span class="sxs-lookup"><span data-stu-id="820d5-831">If you want to switch to a TPH inheritance strategy with Model First, you can use the "Entity Designer Database Generation Power Pack" available from the Visual Studio Gallery ( \<http://visualstudiogallery.msdn.microsoft.com/df3541c3-d833-4b65-b942-989e7ec74c87/>).</span></span>

<span data-ttu-id="820d5-832">當您使用 Code First 設定具有繼承的模型對應時，EF 預設會使用 TPH，因此，繼承階層架構中的所有實體都會對應至相同的資料表。</span><span class="sxs-lookup"><span data-stu-id="820d5-832">When using Code First to configure the mapping of a model with inheritance, EF will use TPH by default, therefore all entities in the inheritance hierarchy will be mapped to the same table.</span></span> <span data-ttu-id="820d5-833">如需詳細資訊，請參閱 MSDN 雜誌（ [http://msdn.microsoft.com/magazine/hh126815.aspx](https://msdn.microsoft.com/magazine/hh126815.aspx)）中 "Code First Entity Framework 4.1" 一節中的「與流暢 API 的對應」一節。</span><span class="sxs-lookup"><span data-stu-id="820d5-833">See the "Mapping with the Fluent API" section of the "Code First in Entity Framework4.1" article in MSDN Magazine ( [http://msdn.microsoft.com/magazine/hh126815.aspx](https://msdn.microsoft.com/magazine/hh126815.aspx)) for more details.</span></span>

### <a name="72-upgrading-from-ef4-to-improve-model-generation-time"></a><span data-ttu-id="820d5-834">7.2 從 EF4 升級以改善模型產生時間</span><span class="sxs-lookup"><span data-stu-id="820d5-834">7.2       Upgrading from EF4 to improve model generation time</span></span>

<span data-ttu-id="820d5-835">在 Entity Framework 5 和6中可取得產生模型之存放層（SSDL）之演算法的 SQL Server 特定改進，並在安裝 Visual Studio 2010 SP1 時，做為 Entity Framework 4 的更新。</span><span class="sxs-lookup"><span data-stu-id="820d5-835">A SQL Server-specific improvement to the algorithm that generates the store-layer (SSDL) of the model is available in Entity Framework 5 and 6, and as an update to Entity Framework 4 when Visual Studio 2010 SP1 is installed.</span></span> <span data-ttu-id="820d5-836">下列測試結果示範產生非常大的模型時的改進，在此案例中為 Navision 模型。</span><span class="sxs-lookup"><span data-stu-id="820d5-836">The following test results demonstrate the improvement when generating a very big model, in this case the Navision model.</span></span> <span data-ttu-id="820d5-837">如需更多詳細資料，請參閱附錄 C。</span><span class="sxs-lookup"><span data-stu-id="820d5-837">See Appendix C for more details about it.</span></span>

<span data-ttu-id="820d5-838">此模型包含1005個實體集和4227個關聯集。</span><span class="sxs-lookup"><span data-stu-id="820d5-838">The model contains 1005 entity sets and 4227 association sets.</span></span>

| <span data-ttu-id="820d5-839">組態</span><span class="sxs-lookup"><span data-stu-id="820d5-839">Configuration</span></span>                              | <span data-ttu-id="820d5-840">已耗用時間的細目</span><span class="sxs-lookup"><span data-stu-id="820d5-840">Breakdown of time consumed</span></span>                                                                                                                                               |
|:-------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="820d5-841">Visual Studio 2010，Entity Framework 4</span><span class="sxs-lookup"><span data-stu-id="820d5-841">Visual Studio 2010, Entity Framework 4</span></span>     | <span data-ttu-id="820d5-842">SSDL 產生：2小時27分鐘</span><span class="sxs-lookup"><span data-stu-id="820d5-842">SSDL Generation: 2 hr 27 min</span></span> <br/> <span data-ttu-id="820d5-843">對應產生：1秒</span><span class="sxs-lookup"><span data-stu-id="820d5-843">Mapping Generation: 1 second</span></span> <br/> <span data-ttu-id="820d5-844">CSDL 產生：1秒</span><span class="sxs-lookup"><span data-stu-id="820d5-844">CSDL Generation: 1 second</span></span> <br/> <span data-ttu-id="820d5-845">ObjectLayer 產生：1秒</span><span class="sxs-lookup"><span data-stu-id="820d5-845">ObjectLayer Generation: 1 second</span></span> <br/> <span data-ttu-id="820d5-846">視圖產生：2小時14分鐘</span><span class="sxs-lookup"><span data-stu-id="820d5-846">View Generation: 2 h 14 min</span></span> |
| <span data-ttu-id="820d5-847">Visual Studio 2010 SP1，Entity Framework 4</span><span class="sxs-lookup"><span data-stu-id="820d5-847">Visual Studio 2010 SP1, Entity Framework 4</span></span> | <span data-ttu-id="820d5-848">SSDL 產生：1秒</span><span class="sxs-lookup"><span data-stu-id="820d5-848">SSDL Generation: 1 second</span></span> <br/> <span data-ttu-id="820d5-849">對應產生：1秒</span><span class="sxs-lookup"><span data-stu-id="820d5-849">Mapping Generation: 1 second</span></span> <br/> <span data-ttu-id="820d5-850">CSDL 產生：1秒</span><span class="sxs-lookup"><span data-stu-id="820d5-850">CSDL Generation: 1 second</span></span> <br/> <span data-ttu-id="820d5-851">ObjectLayer 產生：1秒</span><span class="sxs-lookup"><span data-stu-id="820d5-851">ObjectLayer Generation: 1 second</span></span> <br/> <span data-ttu-id="820d5-852">視圖產生：1小時53分鐘</span><span class="sxs-lookup"><span data-stu-id="820d5-852">View Generation: 1 hr 53 min</span></span>   |
| <span data-ttu-id="820d5-853">Visual Studio 2013，Entity Framework 5</span><span class="sxs-lookup"><span data-stu-id="820d5-853">Visual Studio 2013, Entity Framework 5</span></span>     | <span data-ttu-id="820d5-854">SSDL 產生：1秒</span><span class="sxs-lookup"><span data-stu-id="820d5-854">SSDL Generation: 1 second</span></span> <br/> <span data-ttu-id="820d5-855">對應產生：1秒</span><span class="sxs-lookup"><span data-stu-id="820d5-855">Mapping Generation: 1 second</span></span> <br/> <span data-ttu-id="820d5-856">CSDL 產生：1秒</span><span class="sxs-lookup"><span data-stu-id="820d5-856">CSDL Generation: 1 second</span></span> <br/> <span data-ttu-id="820d5-857">ObjectLayer 產生：1秒</span><span class="sxs-lookup"><span data-stu-id="820d5-857">ObjectLayer Generation: 1 second</span></span> <br/> <span data-ttu-id="820d5-858">視圖產生：65分鐘</span><span class="sxs-lookup"><span data-stu-id="820d5-858">View Generation: 65 minutes</span></span>    |
| <span data-ttu-id="820d5-859">Visual Studio 2013，Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="820d5-859">Visual Studio 2013, Entity Framework 6</span></span>     | <span data-ttu-id="820d5-860">SSDL 產生：1秒</span><span class="sxs-lookup"><span data-stu-id="820d5-860">SSDL Generation: 1 second</span></span> <br/> <span data-ttu-id="820d5-861">對應產生：1秒</span><span class="sxs-lookup"><span data-stu-id="820d5-861">Mapping Generation: 1 second</span></span> <br/> <span data-ttu-id="820d5-862">CSDL 產生：1秒</span><span class="sxs-lookup"><span data-stu-id="820d5-862">CSDL Generation: 1 second</span></span> <br/> <span data-ttu-id="820d5-863">ObjectLayer 產生：1秒</span><span class="sxs-lookup"><span data-stu-id="820d5-863">ObjectLayer Generation: 1 second</span></span> <br/> <span data-ttu-id="820d5-864">視圖產生：28秒。</span><span class="sxs-lookup"><span data-stu-id="820d5-864">View Generation: 28 seconds.</span></span>   |


<span data-ttu-id="820d5-865">值得注意的是，當產生 SSDL 時，負載幾乎會花在 SQL Server 上，而用戶端開發機器則是等待閒置，讓結果回到伺服器。</span><span class="sxs-lookup"><span data-stu-id="820d5-865">It's worth noting that when generating the SSDL, the load is almost entirely spent on the SQL Server, while the client development machine is waiting idle for results to come back from the server.</span></span> <span data-ttu-id="820d5-866">Dba 應該特別感謝這方面的改進。</span><span class="sxs-lookup"><span data-stu-id="820d5-866">DBAs should particularly appreciate this improvement.</span></span> <span data-ttu-id="820d5-867">另外值得一提的是，基本上，模型產生的整體成本會在現在的視圖產生中進行。</span><span class="sxs-lookup"><span data-stu-id="820d5-867">It's also worth noting that essentially the entire cost of model generation takes place in View Generation now.</span></span>

### <a name="73-splitting-large-models-with-database-first-and-model-first"></a><span data-ttu-id="820d5-868">7.3 使用 Database First 和 Model First 分割大型模型</span><span class="sxs-lookup"><span data-stu-id="820d5-868">7.3       Splitting Large Models with Database First and Model First</span></span>

<span data-ttu-id="820d5-869">隨著模型大小的增加，設計介面會變得雜亂，而且很容易使用。</span><span class="sxs-lookup"><span data-stu-id="820d5-869">As model size increases, the designer surface becomes cluttered and difficult to use.</span></span> <span data-ttu-id="820d5-870">我們通常會考慮具有超過300個實體的模型太大，而無法有效使用設計工具。</span><span class="sxs-lookup"><span data-stu-id="820d5-870">We typically consider a model with more than 300 entities to be too large to effectively use the designer.</span></span> <span data-ttu-id="820d5-871">下列的 blog 文章說明分割大型模型的數個選項： \<http://blogs.msdn.com/b/adonet/archive/2008/11/25/working-with-large-models-in-entity-framework-part-2.aspx>。</span><span class="sxs-lookup"><span data-stu-id="820d5-871">The following blog post describes several options for splitting large models: \<http://blogs.msdn.com/b/adonet/archive/2008/11/25/working-with-large-models-in-entity-framework-part-2.aspx>.</span></span>

<span data-ttu-id="820d5-872">文章是針對第一個 Entity Framework 版本所撰寫，但仍適用這些步驟。</span><span class="sxs-lookup"><span data-stu-id="820d5-872">The post was written for the first version of Entity Framework, but the steps still apply.</span></span>

### <a name="74-performance-considerations-with-the-entity-data-source-control"></a><span data-ttu-id="820d5-873">7.4 實體資料來源控制項的效能考慮</span><span class="sxs-lookup"><span data-stu-id="820d5-873">7.4       Performance considerations with the Entity Data Source Control</span></span>

<span data-ttu-id="820d5-874">我們已瞭解多執行緒效能和壓力測試中的案例，其中使用 EntityDataSource 控制項的 web 應用程式效能大幅 deteriorates。</span><span class="sxs-lookup"><span data-stu-id="820d5-874">We've seen cases in multi-threaded performance and stress tests where the performance of a web application using the EntityDataSource Control deteriorates significantly.</span></span> <span data-ttu-id="820d5-875">根本原因在於，EntityDataSource 會在 Web 應用程式所參考的元件上重複呼叫 MetadataWorkspace. LoadFromAssembly，以探索要當做實體使用的類型。</span><span class="sxs-lookup"><span data-stu-id="820d5-875">The underlying cause is that the EntityDataSource repeatedly calls MetadataWorkspace.LoadFromAssembly on the assemblies referenced by the Web application to discover the types to be used as entities.</span></span>

<span data-ttu-id="820d5-876">解決方法是將 EntityDataSource 的 CoNtextTypeName 設定為衍生 ObjectCoNtext 類別的型別名稱。</span><span class="sxs-lookup"><span data-stu-id="820d5-876">The solution is to set the ContextTypeName of the EntityDataSource to the type name of your derived ObjectContext class.</span></span> <span data-ttu-id="820d5-877">這會關閉掃描實體類型之所有參考元件的機制。</span><span class="sxs-lookup"><span data-stu-id="820d5-877">This turns off the mechanism that scans all referenced assemblies for entity types.</span></span>

<span data-ttu-id="820d5-878">設定 CoNtextTypeName 欄位也可防止 .NET 4.0 中的 EntityDataSource 在無法透過反映從元件載入型別時擲回 ReflectionTypeLoadException 的功能問題。</span><span class="sxs-lookup"><span data-stu-id="820d5-878">Setting the ContextTypeName field also prevents a functional problem where the EntityDataSource in .NET 4.0 throws a ReflectionTypeLoadException when it can't load a type from an assembly via reflection.</span></span> <span data-ttu-id="820d5-879">此問題已在 .NET 4.5 中修正。</span><span class="sxs-lookup"><span data-stu-id="820d5-879">This issue has been fixed in .NET 4.5.</span></span>

### <a name="75-poco-entities-and-change-tracking-proxies"></a><span data-ttu-id="820d5-880">7.5 POCO 實體和變更追蹤 proxy</span><span class="sxs-lookup"><span data-stu-id="820d5-880">7.5       POCO entities and change tracking proxies</span></span>

<span data-ttu-id="820d5-881">Entity Framework 可讓您將自訂資料類別與您的資料模型搭配使用，而不需要對資料類別本身進行任何修改。</span><span class="sxs-lookup"><span data-stu-id="820d5-881">Entity Framework enables you to use custom data classes together with your data model without making any modifications to the data classes themselves.</span></span> <span data-ttu-id="820d5-882">這表示您可以使用「單純」(plain-old) CLR 物件 (POCO)，例如現有的網域物件，加上您的資料模型。</span><span class="sxs-lookup"><span data-stu-id="820d5-882">This means that you can use "plain-old" CLR objects (POCO), such as existing domain objects, with your data model.</span></span> <span data-ttu-id="820d5-883">這些 POCO 資料類別（也稱為非持續性物件）對應至資料模型中所定義的實體，可支援與實體資料模型工具所產生之實體類型相同的查詢、插入、更新和刪除行為。</span><span class="sxs-lookup"><span data-stu-id="820d5-883">These POCO data classes (also known as persistence-ignorant objects), which are mapped to entities that are defined in a data model, support most of the same query, insert, update, and delete behaviors as entity types that are generated by the Entity Data Model tools.</span></span>

<span data-ttu-id="820d5-884">Entity Framework 也可以建立衍生自 POCO 類型的 proxy 類別，當您想要在 POCO 實體上啟用消極式載入和自動變更追蹤之類的功能時，就會使用它們。</span><span class="sxs-lookup"><span data-stu-id="820d5-884">Entity Framework can also create proxy classes derived from your POCO types, which are used when you want to enable features such as lazy loading and automatic change tracking on POCO entities.</span></span> <span data-ttu-id="820d5-885">您的 POCO 類別必須符合特定需求，才能允許 Entity Framework 使用 proxy，如下所述： [http://msdn.microsoft.com/library/dd468057.aspx](https://msdn.microsoft.com/library/dd468057.aspx)。</span><span class="sxs-lookup"><span data-stu-id="820d5-885">Your POCO classes must meet certain requirements to allow Entity Framework to use proxies, as described here: [http://msdn.microsoft.com/library/dd468057.aspx](https://msdn.microsoft.com/library/dd468057.aspx).</span></span>

<span data-ttu-id="820d5-886">每當實體的任何屬性變更其值時，追蹤 proxy 就會通知物件狀態管理員，Entity Framework 隨時得知實體的實際狀態。</span><span class="sxs-lookup"><span data-stu-id="820d5-886">Chance tracking proxies will notify the object state manager each time any of the properties of your entities has its value changed, so Entity Framework knows the actual state of your entities all the time.</span></span> <span data-ttu-id="820d5-887">這是藉由將通知事件加入至屬性的 setter 方法主體來完成，並讓物件狀態管理員處理這類事件。</span><span class="sxs-lookup"><span data-stu-id="820d5-887">This is done by adding notification events to the body of the setter methods of your properties, and having the object state manager processing such events.</span></span> <span data-ttu-id="820d5-888">請注意，由於 Entity Framework 所建立的新增事件集，建立 proxy 實體通常會比建立非 proxy POCO 實體更昂貴。</span><span class="sxs-lookup"><span data-stu-id="820d5-888">Note that creating a proxy entity will typically be more expensive than creating a non-proxy POCO entity due to the added set of events created by Entity Framework.</span></span>

<span data-ttu-id="820d5-889">當 POCO 實體沒有變更追蹤 proxy 時，會藉由比較實體的內容與先前儲存的狀態複本來找到變更。</span><span class="sxs-lookup"><span data-stu-id="820d5-889">When a POCO entity does not have a change tracking proxy, changes are found by comparing the contents of your entities against a copy of a previous saved state.</span></span> <span data-ttu-id="820d5-890">當您的內容中有許多實體，或當您的實體有非常大量的屬性時（即使自上次進行比較之後，它們都不會變更），此深層比較會變得很冗長。</span><span class="sxs-lookup"><span data-stu-id="820d5-890">This deep comparison will become a lengthy process when you have many entities in your context, or when your entities have a very large amount of properties, even if none of them changed since the last comparison took place.</span></span>

<span data-ttu-id="820d5-891">摘要說明：當您建立變更追蹤 proxy 時，將會對效能造成的影響，但當您的實體有許多屬性或您的模型中有多個實體時，變更追蹤將可協助您加速變更偵測程式。</span><span class="sxs-lookup"><span data-stu-id="820d5-891">In summary: you’ll pay a performance hit when creating the change tracking proxy, but change tracking will help you speed up the change detection process when your entities have many properties or when you have many entities in your model.</span></span> <span data-ttu-id="820d5-892">對於具有少數屬性的實體，其中實體數量不會變得太多，具有變更追蹤 proxy 的好處可能不多。</span><span class="sxs-lookup"><span data-stu-id="820d5-892">For entities with a small number of properties where the amount of entities doesn’t grow too much, having change tracking proxies may not be of much benefit.</span></span>

## <a name="8-loading-related-entities"></a><span data-ttu-id="820d5-893">8載入相關實體</span><span class="sxs-lookup"><span data-stu-id="820d5-893">8 Loading Related Entities</span></span>

### <a name="81-lazy-loading-vs-eager-loading"></a><span data-ttu-id="820d5-894">8.1 消極式載入與積極式載入</span><span class="sxs-lookup"><span data-stu-id="820d5-894">8.1 Lazy Loading vs. Eager Loading</span></span>

<span data-ttu-id="820d5-895">Entity Framework 提供幾種不同的方式來載入與您的目標實體相關的實體。</span><span class="sxs-lookup"><span data-stu-id="820d5-895">Entity Framework offers several different ways to load the entities that are related to your target entity.</span></span> <span data-ttu-id="820d5-896">例如，當您查詢產品時，會有不同的方式可將相關的訂單載入物件狀態管理員。</span><span class="sxs-lookup"><span data-stu-id="820d5-896">For example, when you query for Products, there are different ways that the related Orders will be loaded into the Object State Manager.</span></span> <span data-ttu-id="820d5-897">從效能的觀點來看，載入相關實體時要考慮的最大問題，就是要使用消極式載入還是積極式載入。</span><span class="sxs-lookup"><span data-stu-id="820d5-897">From a performance standpoint, the biggest question to consider when loading related entities will be whether to use Lazy Loading or Eager Loading.</span></span>

<span data-ttu-id="820d5-898">使用積極式載入時，相關的實體會與您的目標實體集一併載入。</span><span class="sxs-lookup"><span data-stu-id="820d5-898">When using Eager Loading, the related entities are loaded along with your target entity set.</span></span> <span data-ttu-id="820d5-899">您在查詢中使用 Include 語句，以指出您想要帶入哪些相關實體。</span><span class="sxs-lookup"><span data-stu-id="820d5-899">You use an Include statement in your query to indicate which related entities you want to bring in.</span></span>

<span data-ttu-id="820d5-900">使用消極式載入時，您的初始查詢只會帶入目標實體集。</span><span class="sxs-lookup"><span data-stu-id="820d5-900">When using Lazy Loading, your initial query only brings in the target entity set.</span></span> <span data-ttu-id="820d5-901">但是每當您存取導覽屬性時，就會對存放區發出另一個查詢，以載入相關的實體。</span><span class="sxs-lookup"><span data-stu-id="820d5-901">But whenever you access a navigation property, another query is issued against the store to load the related entity.</span></span>

<span data-ttu-id="820d5-902">載入實體之後，任何進一步的實體查詢都會直接從物件狀態管理員載入，不論您使用的是消極式載入或積極式載入。</span><span class="sxs-lookup"><span data-stu-id="820d5-902">Once an entity has been loaded, any further queries for the entity will load it directly from the Object State Manager, whether you are using lazy loading or eager loading.</span></span>

### <a name="82-how-to-choose-between-lazy-loading-and-eager-loading"></a><span data-ttu-id="820d5-903">8.2 如何選擇消極式載入和積極式載入</span><span class="sxs-lookup"><span data-stu-id="820d5-903">8.2 How to choose between Lazy Loading and Eager Loading</span></span>

<span data-ttu-id="820d5-904">重要的是，您瞭解消極式載入和立即載入之間的差異，讓您可以為應用程式做出正確的選擇。</span><span class="sxs-lookup"><span data-stu-id="820d5-904">The important thing is that you understand the difference between Lazy Loading and Eager Loading so that you can make the correct choice for your application.</span></span> <span data-ttu-id="820d5-905">這可協助您評估對資料庫的多個要求與可能包含大型承載的單一要求之間的取捨。</span><span class="sxs-lookup"><span data-stu-id="820d5-905">This will help you evaluate the tradeoff between multiple requests against the database versus a single request that may contain a large payload.</span></span> <span data-ttu-id="820d5-906">可能適合在應用程式的某些部分使用積極式載入，並在其他部分中進行消極式載入。</span><span class="sxs-lookup"><span data-stu-id="820d5-906">It may be appropriate to use eager loading in some parts of your application and lazy loading in other parts.</span></span>

<span data-ttu-id="820d5-907">舉例來說，假設您想要查詢居住在英國的客戶及其訂單計數，這就是一個例子。</span><span class="sxs-lookup"><span data-stu-id="820d5-907">As an example of what's happening under the hood, suppose you want to query for the customers who live in the UK and their order count.</span></span>

<span data-ttu-id="820d5-908">**使用積極式載入**</span><span class="sxs-lookup"><span data-stu-id="820d5-908">**Using Eager Loading**</span></span>

``` csharp
using (NorthwindEntities context = new NorthwindEntities())
{
    var ukCustomers = context.Customers.Include(c => c.Orders).Where(c => c.Address.Country == "UK");
    var chosenCustomer = AskUserToPickCustomer(ukCustomers);
    Console.WriteLine("Customer Id: {0} has {1} orders", customer.CustomerID, customer.Orders.Count);
}
```

<span data-ttu-id="820d5-909">**使用消極式載入**</span><span class="sxs-lookup"><span data-stu-id="820d5-909">**Using Lazy Loading**</span></span>

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

<span data-ttu-id="820d5-910">使用積極式載入時，您會發出傳回所有客戶和所有訂單的單一查詢。</span><span class="sxs-lookup"><span data-stu-id="820d5-910">When using eager loading, you'll issue a single query that returns all customers and all orders.</span></span> <span data-ttu-id="820d5-911">Store 命令看起來像這樣：</span><span class="sxs-lookup"><span data-stu-id="820d5-911">The store command looks like:</span></span>

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

<span data-ttu-id="820d5-912">使用消極式載入時，您會一開始就發出下列查詢：</span><span class="sxs-lookup"><span data-stu-id="820d5-912">When using lazy loading, you'll issue the following query initially:</span></span>

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

<span data-ttu-id="820d5-913">而且每次您存取客戶的 Orders 導覽屬性時，都會針對該商店發出另一個類似下列的查詢：</span><span class="sxs-lookup"><span data-stu-id="820d5-913">And each time you access the Orders navigation property of a customer another query like the following is issued against the store:</span></span>

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

<span data-ttu-id="820d5-914">如需詳細資訊，請參閱[載入相關物件](https://msdn.microsoft.com/library/bb896272.aspx)。</span><span class="sxs-lookup"><span data-stu-id="820d5-914">For more information, see the [Loading Related Objects](https://msdn.microsoft.com/library/bb896272.aspx).</span></span>

#### <a name="821-lazy-loading-versus-eager-loading-cheat-sheet"></a><span data-ttu-id="820d5-915">8.2.1 消極式載入與積極式載入功能提要</span><span class="sxs-lookup"><span data-stu-id="820d5-915">8.2.1 Lazy Loading versus Eager Loading cheat sheet</span></span>

<span data-ttu-id="820d5-916">其中沒有任何一種適用于選擇積極式載入和消極式載入的東西。</span><span class="sxs-lookup"><span data-stu-id="820d5-916">There’s no such thing as a one-size-fits-all to choosing eager loading versus lazy loading.</span></span> <span data-ttu-id="820d5-917">請先嘗試瞭解這兩種策略之間的差異，讓您可以進行明智的決策;此外，請考慮您的程式碼是否符合下列任一案例：</span><span class="sxs-lookup"><span data-stu-id="820d5-917">Try first to understand the differences between both strategies so you can do a well informed decision; also, consider if your code fits to any of the following scenarios:</span></span>

| <span data-ttu-id="820d5-918">狀況</span><span class="sxs-lookup"><span data-stu-id="820d5-918">Scenario</span></span>                                                                    | <span data-ttu-id="820d5-919">我們的建議</span><span class="sxs-lookup"><span data-stu-id="820d5-919">Our Suggestion</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
|:----------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="820d5-920">您需要從提取的實體存取許多導覽屬性嗎？</span><span class="sxs-lookup"><span data-stu-id="820d5-920">Do you need to access many navigation properties from the fetched entities?</span></span> | <span data-ttu-id="820d5-921">**否**-這兩個選項都可能會這麼做。</span><span class="sxs-lookup"><span data-stu-id="820d5-921">**No** - Both options will probably do.</span></span> <span data-ttu-id="820d5-922">不過，如果您的查詢所帶入的承載不太大，您可能會因為使用立即載入而遇到效能上的好處，因為它需要較少的網路往返，才能具體化您的物件。</span><span class="sxs-lookup"><span data-stu-id="820d5-922">However, if the payload your query is bringing is not too big, you may experience performance benefits by using Eager loading as it’ll require less network round trips to materialize your objects.</span></span> <br/> <br/> <span data-ttu-id="820d5-923">**是**-如果您需要從實體存取許多導覽屬性，您可以在查詢中使用多個 include 語句搭配積極式載入來執行此動作。</span><span class="sxs-lookup"><span data-stu-id="820d5-923">**Yes** -  If you need to access many navigation properties from the entities, you’d do that by using multiple include statements in your query with Eager loading.</span></span> <span data-ttu-id="820d5-924">您所包含的實體越多，您的查詢將會傳回的承載越大。</span><span class="sxs-lookup"><span data-stu-id="820d5-924">The more entities you include, the bigger the payload your query will return.</span></span> <span data-ttu-id="820d5-925">一旦您在查詢中包含三個或多個實體，請考慮切換為消極式載入。</span><span class="sxs-lookup"><span data-stu-id="820d5-925">Once you include three or more entities into your query, consider switching to Lazy loading.</span></span> |
| <span data-ttu-id="820d5-926">您是否知道在執行時間需要哪些資料？</span><span class="sxs-lookup"><span data-stu-id="820d5-926">Do you know exactly what data will be needed at run time?</span></span>                   | <span data-ttu-id="820d5-927">**否**-延遲載入會更適合您。</span><span class="sxs-lookup"><span data-stu-id="820d5-927">**No** - Lazy loading will be better for you.</span></span> <span data-ttu-id="820d5-928">否則，您最後可能會查詢不需要的資料。</span><span class="sxs-lookup"><span data-stu-id="820d5-928">Otherwise, you may end up querying for data that you will not need.</span></span> <br/> <br/> <span data-ttu-id="820d5-929">**是**-積極式載入可能是最好的選擇;這將有助於更快速地載入整個集合。</span><span class="sxs-lookup"><span data-stu-id="820d5-929">**Yes** - Eager loading is probably your best bet; it will help loading entire sets faster.</span></span> <span data-ttu-id="820d5-930">如果您的查詢需要提取非常大量的資料，而這變得太慢，請改為嘗試消極式載入。</span><span class="sxs-lookup"><span data-stu-id="820d5-930">If your query requires fetching a very large amount of data, and this becomes too slow, then try Lazy loading instead.</span></span>                                                                                                                                                                                                                                                       |
| <span data-ttu-id="820d5-931">您的程式碼在您的資料庫中執行得很遠嗎？</span><span class="sxs-lookup"><span data-stu-id="820d5-931">Is your code executing far from your database?</span></span> <span data-ttu-id="820d5-932">（網路延遲增加）</span><span class="sxs-lookup"><span data-stu-id="820d5-932">(increased network latency)</span></span>  | <span data-ttu-id="820d5-933">**否**-當網路延遲不是問題時，使用消極式載入可能會簡化您的程式碼。</span><span class="sxs-lookup"><span data-stu-id="820d5-933">**No** - When the network latency is not an issue, using Lazy loading may simplify your code.</span></span> <span data-ttu-id="820d5-934">請記住，您的應用程式拓撲可能會變更，因此不會將資料庫的鄰近性授與。</span><span class="sxs-lookup"><span data-stu-id="820d5-934">Remember that the topology of your application may change, so don’t take database proximity for granted.</span></span> <br/> <br/> <span data-ttu-id="820d5-935">**是**-當網路發生問題時，只有您可以決定適合您案例的內容為何。</span><span class="sxs-lookup"><span data-stu-id="820d5-935">**Yes** - When the network is a problem, only you can decide what fits better for your scenario.</span></span> <span data-ttu-id="820d5-936">通常積極式載入會比較好，因為它需要較少的來回行程。</span><span class="sxs-lookup"><span data-stu-id="820d5-936">Typically Eager loading will be better because it requires fewer round trips.</span></span>                                                                                                                                                                                                      |


#### <a name="822-performance-concerns-with-multiple-includes"></a><span data-ttu-id="820d5-937">使用多個包含的8.2.2 效能考慮</span><span class="sxs-lookup"><span data-stu-id="820d5-937">8.2.2       Performance concerns with multiple Includes</span></span>

<span data-ttu-id="820d5-938">當我們聽到涉及伺服器回應時間問題的效能問題時，問題的來源經常是使用多個 Include 語句來查詢。</span><span class="sxs-lookup"><span data-stu-id="820d5-938">When we hear performance questions that involve server response time problems, the source of the issue is frequently queries with multiple Include statements.</span></span> <span data-ttu-id="820d5-939">雖然在查詢中包含相關實體很強大，但請務必瞭解幕後的情況。</span><span class="sxs-lookup"><span data-stu-id="820d5-939">While including related entities in a query is powerful, it's important to understand what's happening under the covers.</span></span>

<span data-ttu-id="820d5-940">具有多個 Include 語句的查詢會花相當長的時間來執行內部計畫編譯器，以產生 store 命令。</span><span class="sxs-lookup"><span data-stu-id="820d5-940">It takes a relatively long time for a query with multiple Include statements in it to go through our internal plan compiler to produce the store command.</span></span> <span data-ttu-id="820d5-941">這段期間的大部分時間都花在嘗試優化產生的查詢。</span><span class="sxs-lookup"><span data-stu-id="820d5-941">The majority of this time is spent trying to optimize the resulting query.</span></span> <span data-ttu-id="820d5-942">根據您的對應而定，產生的存放區命令將包含每個包含的外部聯結或聯集。</span><span class="sxs-lookup"><span data-stu-id="820d5-942">The generated store command will contain an Outer Join or Union for each Include, depending on your mapping.</span></span> <span data-ttu-id="820d5-943">這類查詢會在單一裝載中帶入來自您資料庫的大型連線圖形，這會 acerbate 任何頻寬問題，尤其是在承載中有很多的冗余時（例如，當您使用多個包含層級來進行遍歷時）。一對多方向的關聯）。</span><span class="sxs-lookup"><span data-stu-id="820d5-943">Queries like this will bring in large connected graphs from your database in a single payload, which will acerbate any bandwidth issues, especially when there is a lot of redundancy in the payload (for example, when multiple levels of Include are used to traverse associations in the one-to-many direction).</span></span>

<span data-ttu-id="820d5-944">您可以使用 ToTraceString 並在 SQL Server Management Studio 中執行存放區命令來查看裝載大小，藉以檢查查詢是否傳回過大的承載的情況。</span><span class="sxs-lookup"><span data-stu-id="820d5-944">You can check for cases where your queries are returning excessively large payloads by accessing the underlying TSQL for the query by using ToTraceString and executing the store command in SQL Server Management Studio to see the payload size.</span></span> <span data-ttu-id="820d5-945">在這種情況下，您可以嘗試減少查詢中的 Include 語句數目，只帶入您所需的資料。</span><span class="sxs-lookup"><span data-stu-id="820d5-945">In such cases you can try to reduce the number of Include statements in your query to just bring in the data you need.</span></span> <span data-ttu-id="820d5-946">或者，您可以將查詢分成較小的子查詢順序，例如：</span><span class="sxs-lookup"><span data-stu-id="820d5-946">Or you may be able to break your query into a smaller sequence of subqueries, for example:</span></span>

<span data-ttu-id="820d5-947">**在中斷查詢之前：**</span><span class="sxs-lookup"><span data-stu-id="820d5-947">**Before breaking the query:**</span></span>

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

<span data-ttu-id="820d5-948">**中斷查詢之後：**</span><span class="sxs-lookup"><span data-stu-id="820d5-948">**After breaking the query:**</span></span>

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

<span data-ttu-id="820d5-949">這僅適用于追蹤的查詢，因為我們會利用內容必須自動執行識別解析和關聯修復的功能。</span><span class="sxs-lookup"><span data-stu-id="820d5-949">This will work only on tracked queries, as we are making use of the ability the context has to perform identity resolution and association fixup automatically.</span></span>

<span data-ttu-id="820d5-950">如同消極式載入，代價是較小的承載會有更多的查詢。</span><span class="sxs-lookup"><span data-stu-id="820d5-950">As with lazy loading, the tradeoff will be more queries for smaller payloads.</span></span> <span data-ttu-id="820d5-951">您也可以使用個別屬性的投影，從每個實體明確選取您需要的資料，但在此情況下，您將不會載入實體，而且不會支援更新。</span><span class="sxs-lookup"><span data-stu-id="820d5-951">You can also use projections of individual properties to explicitly select only the data you need from each entity, but you will not be loading entities in this case, and updates will not be supported.</span></span>

#### <a name="823-workaround-to-get-lazy-loading-of-properties"></a><span data-ttu-id="820d5-952">8.2.3 因應措施以取得屬性的消極式載入</span><span class="sxs-lookup"><span data-stu-id="820d5-952">8.2.3 Workaround to get lazy loading of properties</span></span>

<span data-ttu-id="820d5-953">Entity Framework 目前不支援純量或複雜屬性的消極式載入。</span><span class="sxs-lookup"><span data-stu-id="820d5-953">Entity Framework currently doesn’t support lazy loading of scalar or complex properties.</span></span> <span data-ttu-id="820d5-954">不過，如果您的資料表包含大型物件（例如 BLOB），您可以使用資料表分割，將大型屬性分隔成不同的實體。</span><span class="sxs-lookup"><span data-stu-id="820d5-954">However, in cases where you have a table that includes a large object such as a BLOB, you can use table splitting to separate the large properties into a separate entity.</span></span> <span data-ttu-id="820d5-955">例如，假設您有一個包含 Varbinary 相片資料行的產品資料表。</span><span class="sxs-lookup"><span data-stu-id="820d5-955">For example, suppose you have a Product table that includes a varbinary photo column.</span></span> <span data-ttu-id="820d5-956">如果您不常需要在查詢中存取此屬性，您可以使用資料表分割，只帶入您通常需要的實體部分。</span><span class="sxs-lookup"><span data-stu-id="820d5-956">If you don't frequently need to access this property in your queries, you can use table splitting to bring in only the parts of the entity that you normally need.</span></span> <span data-ttu-id="820d5-957">代表產品相片的實體只有在您明確需要時才會載入。</span><span class="sxs-lookup"><span data-stu-id="820d5-957">The entity representing the product photo will only be loaded when you explicitly need it.</span></span>

<span data-ttu-id="820d5-958">如需如何啟用資料表分割的良好資源，請 Gil Fink 的「Entity Framework 中的資料表分割」 blog 文章： \<http://blogs.microsoft.co.il/blogs/gilf/archive/2009/10/13/table-splitting-in-entity-framework.aspx>。</span><span class="sxs-lookup"><span data-stu-id="820d5-958">A good resource that shows how to enable table splitting is Gil Fink's "Table Splitting in Entity Framework" blog post: \<http://blogs.microsoft.co.il/blogs/gilf/archive/2009/10/13/table-splitting-in-entity-framework.aspx>.</span></span>

## <a name="9-other-considerations"></a><span data-ttu-id="820d5-959">9其他考慮</span><span class="sxs-lookup"><span data-stu-id="820d5-959">9 Other considerations</span></span>

### <a name="91-server-garbage-collection"></a><span data-ttu-id="820d5-960">9.1 伺服器垃圾收集</span><span class="sxs-lookup"><span data-stu-id="820d5-960">9.1      Server Garbage Collection</span></span>

<span data-ttu-id="820d5-961">某些使用者可能會遇到資源爭用，限制當垃圾收集行程未正確設定時，所預期的平行處理原則。</span><span class="sxs-lookup"><span data-stu-id="820d5-961">Some users might experience resource contention that limits the parallelism they are expecting when the Garbage Collector is not properly configured.</span></span> <span data-ttu-id="820d5-962">每當 EF 用於多執行緒案例中，或在類似伺服器端系統的任何應用程式中，請務必啟用伺服器垃圾收集。</span><span class="sxs-lookup"><span data-stu-id="820d5-962">Whenever EF is used in a multithreaded scenario, or in any application that resembles a server-side system, make sure to enable Server Garbage Collection.</span></span> <span data-ttu-id="820d5-963">這項作業是透過應用程式佈建檔中的簡單設定來完成：</span><span class="sxs-lookup"><span data-stu-id="820d5-963">This is done via a simple setting in your application config file:</span></span>

``` xml
<?xmlversion="1.0" encoding="utf-8" ?>
<configuration>
        <runtime>
               <gcServer enabled="true" />
        </runtime>
</configuration>
```

<span data-ttu-id="820d5-964">這應該會減少執行緒爭用，並增加您的輸送量，最高可達30% 的 CPU 飽和案例。</span><span class="sxs-lookup"><span data-stu-id="820d5-964">This should decrease your thread contention and increase your throughput by up to 30% in CPU saturated scenarios.</span></span> <span data-ttu-id="820d5-965">一般來說，您應該一律使用傳統垃圾收集（針對 UI 和用戶端案例較佳的微調）以及伺服器垃圾收集，來測試應用程式的行為。</span><span class="sxs-lookup"><span data-stu-id="820d5-965">In general terms, you should always test how your application behaves using the classic Garbage Collection (which is better tuned for UI and client side scenarios) as well as the Server Garbage Collection.</span></span>

### <a name="92-autodetectchanges"></a><span data-ttu-id="820d5-966">9.2 AutoDetectChanges</span><span class="sxs-lookup"><span data-stu-id="820d5-966">9.2      AutoDetectChanges</span></span>

<span data-ttu-id="820d5-967">如先前所述，當物件快取有許多實體時，Entity Framework 可能會顯示效能問題。</span><span class="sxs-lookup"><span data-stu-id="820d5-967">As mentioned earlier, Entity Framework might show performance issues when the object cache has many entities.</span></span> <span data-ttu-id="820d5-968">某些作業（例如新增、移除、尋找、輸入和 SaveChanges）會觸發對 DetectChanges 的呼叫，這可能會根據物件快取的大小而耗用大量的 CPU。</span><span class="sxs-lookup"><span data-stu-id="820d5-968">Certain operations, such as Add, Remove, Find, Entry and SaveChanges, trigger calls to DetectChanges which might consume a large amount of CPU based on how large the object cache has become.</span></span> <span data-ttu-id="820d5-969">這是因為物件快取和物件狀態管理員在每個執行到內容的作業上都嘗試保持同步處理，因此在各種情況下，會保證產生的資料是正確的。</span><span class="sxs-lookup"><span data-stu-id="820d5-969">The reason for this is that the object cache and the object state manager try to stay as synchronized as possible on each operation performed to a context so that the produced data is guaranteed to be correct under a wide array of scenarios.</span></span>

<span data-ttu-id="820d5-970">通常在應用程式的整個生命週期中，保留 Entity Framework 的自動變更偵測是個不錯的作法。</span><span class="sxs-lookup"><span data-stu-id="820d5-970">It is generally a good practice to leave Entity Framework’s automatic change detection enabled for the entire life of your application.</span></span> <span data-ttu-id="820d5-971">如果您的案例受到高 CPU 使用量的負面影響，且您的設定檔指出問題是 DetectChanges 的呼叫，請考慮暫時關閉程式碼敏感部分中的 AutoDetectChanges：</span><span class="sxs-lookup"><span data-stu-id="820d5-971">If your scenario is being negatively affected by high CPU usage and your profiles indicate that the culprit is the call to DetectChanges, consider temporarily turning off AutoDetectChanges in the sensitive portion of your code:</span></span>

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

<span data-ttu-id="820d5-972">在關閉 AutoDetectChanges 之前，最好先瞭解這可能會導致 Entity Framework 失去追蹤實體所發生變更之特定資訊的能力。</span><span class="sxs-lookup"><span data-stu-id="820d5-972">Before turning off AutoDetectChanges, it’s good to understand that this might cause Entity Framework to lose its ability to track certain information about the changes that are taking place on the entities.</span></span> <span data-ttu-id="820d5-973">如果處理錯誤，這可能會造成應用程式的資料不一致。</span><span class="sxs-lookup"><span data-stu-id="820d5-973">If handled incorrectly, this might cause data inconsistency on your application.</span></span> <span data-ttu-id="820d5-974">如需關閉 AutoDetectChanges 的詳細資訊，請參閱 \<http://blog.oneunicorn.com/2012/03/12/secrets-of-detectchanges-part-3-switching-off-automatic-detectchanges/>。</span><span class="sxs-lookup"><span data-stu-id="820d5-974">For more information on turning off AutoDetectChanges, read \<http://blog.oneunicorn.com/2012/03/12/secrets-of-detectchanges-part-3-switching-off-automatic-detectchanges/>.</span></span>

### <a name="93-context-per-request"></a><span data-ttu-id="820d5-975">9.3 每個要求的內容</span><span class="sxs-lookup"><span data-stu-id="820d5-975">9.3      Context per request</span></span>

<span data-ttu-id="820d5-976">Entity Framework 的內容應該當做短期實例使用，以提供最佳的效能體驗。</span><span class="sxs-lookup"><span data-stu-id="820d5-976">Entity Framework’s contexts are meant to be used as short-lived instances in order to provide the most optimal performance experience.</span></span> <span data-ttu-id="820d5-977">內容應該是短期的，而且會被捨棄，因此，在可能的情況下，這類內容已實作為非常輕量且 reutilize 的中繼資料。</span><span class="sxs-lookup"><span data-stu-id="820d5-977">Contexts are expected to be short lived and discarded, and as such have been implemented to be very lightweight and reutilize metadata whenever possible.</span></span> <span data-ttu-id="820d5-978">在 web 案例中，請務必記住這一點，而且沒有內容可以超過單一要求的持續時間。</span><span class="sxs-lookup"><span data-stu-id="820d5-978">In web scenarios it’s important to keep this in mind and not have a context for more than the duration of a single request.</span></span> <span data-ttu-id="820d5-979">同樣地，在非 web 案例中，應該根據您對 Entity Framework 中不同快取層級的瞭解來捨棄內容。</span><span class="sxs-lookup"><span data-stu-id="820d5-979">Similarly, in non-web scenarios, context should be discarded based on your understanding of the different levels of caching in the Entity Framework.</span></span> <span data-ttu-id="820d5-980">一般來說，您應該避免在應用程式的整個生命週期中有一個內容實例，以及每個執行緒和一個靜態內容的上下文。</span><span class="sxs-lookup"><span data-stu-id="820d5-980">Generally speaking, one should avoid having a context instance throughout the life of the application, as well as contexts per thread and static contexts.</span></span>

### <a name="94-database-null-semantics"></a><span data-ttu-id="820d5-981">9.4 資料庫 null 語義</span><span class="sxs-lookup"><span data-stu-id="820d5-981">9.4      Database null semantics</span></span>

<span data-ttu-id="820d5-982">Entity Framework 預設會產生具有 C\# null 比較語義的 SQL 程式碼。</span><span class="sxs-lookup"><span data-stu-id="820d5-982">Entity Framework by default will generate SQL code that has C\# null comparison semantics.</span></span> <span data-ttu-id="820d5-983">請考量下列範例查詢：</span><span class="sxs-lookup"><span data-stu-id="820d5-983">Consider the following example query:</span></span>

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

<span data-ttu-id="820d5-984">在此範例中，我們會將數個可為 null 的變數與實體上的可為 null 屬性進行比較，例如，已建立供應專案和單價。</span><span class="sxs-lookup"><span data-stu-id="820d5-984">In this example, we’re comparing a number of nullable variables against nullable properties on the entity, such as SupplierID and UnitPrice.</span></span> <span data-ttu-id="820d5-985">針對此查詢產生的 SQL 會詢問參數值是否與資料行值相同，或者參數和資料行值是否為 null。</span><span class="sxs-lookup"><span data-stu-id="820d5-985">The generated SQL for this query will ask if the parameter value is the same as the column value, or if both the parameter and the column values are null.</span></span> <span data-ttu-id="820d5-986">這會隱藏資料庫伺服器處理 null 的方式，並在不同的資料庫廠商之間提供一致的 C\# null 體驗。</span><span class="sxs-lookup"><span data-stu-id="820d5-986">This will hide the way the database server handles nulls and will provide a consistent C\# null experience across different database vendors.</span></span> <span data-ttu-id="820d5-987">相反地，產生的程式碼會有點複雜，而且當查詢的 where 語句中的比較量成長至大量時，可能無法順利執行。</span><span class="sxs-lookup"><span data-stu-id="820d5-987">On the other hand, the generated code is a bit convoluted and may not perform well when the amount of comparisons in the where statement of the query grows to a large number.</span></span>

<span data-ttu-id="820d5-988">處理這種情況的其中一種方式是使用資料庫 null 語義。</span><span class="sxs-lookup"><span data-stu-id="820d5-988">One way to deal with this situation is by using database null semantics.</span></span> <span data-ttu-id="820d5-989">請注意，這可能會與 C\# null 語義有不同的行為，因為現在 Entity Framework 會產生更簡單的 SQL，以公開資料庫引擎處理 null 值的方式。</span><span class="sxs-lookup"><span data-stu-id="820d5-989">Note that this might potentially behave differently to the C\# null semantics since now Entity Framework will generate simpler SQL that exposes the way the database engine handles null values.</span></span> <span data-ttu-id="820d5-990">針對內容設定，可以針對每個內容啟用資料庫 null 語義，其中單一設定行：</span><span class="sxs-lookup"><span data-stu-id="820d5-990">Database null semantics can be activated per-context with one single configuration line against the context configuration:</span></span>

``` csharp
                context.Configuration.UseDatabaseNullSemantics = true;
```

<span data-ttu-id="820d5-991">使用資料庫 null 語義時，小型到中型的查詢不會顯示明顯的效能改進，但是在具有大量潛在 null 比較的查詢上，其差異會變得更明顯。</span><span class="sxs-lookup"><span data-stu-id="820d5-991">Small to medium sized queries will not display a perceptible performance improvement when using database null semantics, but the difference will become more noticeable on queries with a large number of potential null comparisons.</span></span>

<span data-ttu-id="820d5-992">在上述的範例查詢中，在受控制的環境中執行的 microbenchmark 中，效能差異小於2%。</span><span class="sxs-lookup"><span data-stu-id="820d5-992">In the example query above, the performance difference was less than 2% in a microbenchmark running in a controlled environment.</span></span>

### <a name="95-async"></a><span data-ttu-id="820d5-993">9.5 非同步</span><span class="sxs-lookup"><span data-stu-id="820d5-993">9.5      Async</span></span>

<span data-ttu-id="820d5-994">Entity Framework 6 在 .NET 4.5 或更新版本上執行時，引進了非同步作業的支援。</span><span class="sxs-lookup"><span data-stu-id="820d5-994">Entity Framework 6 introduced support of async operations when running on .NET 4.5 or later.</span></span> <span data-ttu-id="820d5-995">在大部分的情況下，具有 IO 相關爭用的應用程式將從使用非同步查詢和儲存作業中獲益最多。</span><span class="sxs-lookup"><span data-stu-id="820d5-995">For the most part, applications that have IO related contention will benefit the most from using asynchronous query and save operations.</span></span> <span data-ttu-id="820d5-996">如果您的應用程式不受 IO 爭用的影響，在最佳情況下，使用 async 會同步執行，並以同步呼叫的相同時間量傳回結果，或在最壞的情況下，只需順延強制非同步工作並新增額外的 time 完成您的案例。</span><span class="sxs-lookup"><span data-stu-id="820d5-996">If your application does not suffer from IO contention, the use of async will, in the best cases, run synchronously and return the result in the same amount of time as a synchronous call, or in the worst case, simply defer execution to an asynchronous task and add extra time to the completion of your scenario.</span></span>

<span data-ttu-id="820d5-997">如需非同步程式設計工作如何協助您判斷非同步如何改善應用程式效能的詳細資訊，請造訪[http://msdn.microsoft.com/library/hh191443.aspx](https://msdn.microsoft.com/library/hh191443.aspx)。</span><span class="sxs-lookup"><span data-stu-id="820d5-997">For information on how asynchronous programming work that will help you deciding if async will improve the performance of your application visit [http://msdn.microsoft.com/library/hh191443.aspx](https://msdn.microsoft.com/library/hh191443.aspx).</span></span> <span data-ttu-id="820d5-998">如需在 Entity Framework 上使用非同步作業的詳細資訊，請參閱[非同步查詢和儲存](~/ef6/fundamentals/async.md
)。</span><span class="sxs-lookup"><span data-stu-id="820d5-998">For more information on the use of async operations on Entity Framework, see [Async Query and Save](~/ef6/fundamentals/async.md
).</span></span>

### <a name="96-ngen"></a><span data-ttu-id="820d5-999">9.6 NGEN</span><span class="sxs-lookup"><span data-stu-id="820d5-999">9.6      NGEN</span></span>

<span data-ttu-id="820d5-1000">Entity Framework 6 並未隨附于 .NET Framework 的預設安裝。</span><span class="sxs-lookup"><span data-stu-id="820d5-1000">Entity Framework 6 does not come in the default installation of .NET framework.</span></span> <span data-ttu-id="820d5-1001">因此，Entity Framework 元件的預設值不是 NGEN，這表示所有的 Entity Framework 程式碼都受限於與其他任何 MSIL 元件相同的 JIT'ing 成本。</span><span class="sxs-lookup"><span data-stu-id="820d5-1001">As such, the Entity Framework assemblies are not NGEN’d by default which means that all the Entity Framework code is subject to the same JIT’ing costs as any other MSIL assembly.</span></span> <span data-ttu-id="820d5-1002">這可能會在開發時降低 F5 體驗，同時也會在生產環境中執行應用程式的冷啟動。</span><span class="sxs-lookup"><span data-stu-id="820d5-1002">This might degrade the F5 experience while developing and also the cold startup of your application in the production environments.</span></span> <span data-ttu-id="820d5-1003">為了減少 JIT'ing 的 CPU 和記憶體成本，建議您視需要 NGEN Entity Framework 的影像。</span><span class="sxs-lookup"><span data-stu-id="820d5-1003">In order to reduce the CPU and memory costs of JIT’ing it is advisable to NGEN the Entity Framework images as appropriate.</span></span> <span data-ttu-id="820d5-1004">如需如何使用 NGEN 改善 Entity Framework 6 之啟動效能的詳細資訊，請參閱[使用 Ngen 改善啟動效能](~/ef6/fundamentals/performance/ngen.md)。</span><span class="sxs-lookup"><span data-stu-id="820d5-1004">For more information on how to improve the startup performance of Entity Framework 6 with NGEN, see [Improving Startup Performance with NGen](~/ef6/fundamentals/performance/ngen.md).</span></span>

### <a name="97-code-first-versus-edmx"></a><span data-ttu-id="820d5-1005">9.7 Code First 與 EDMX 的比較</span><span class="sxs-lookup"><span data-stu-id="820d5-1005">9.7      Code First versus EDMX</span></span>

<span data-ttu-id="820d5-1006">在物件導向程式設計和關係資料庫之間，透過記憶體內部表示概念模型（物件）、儲存架構（資料庫）和兩者之間的對應，來 Entity Framework 有關阻抗不相符問題的原因兩個.</span><span class="sxs-lookup"><span data-stu-id="820d5-1006">Entity Framework reasons about the impedance mismatch problem between object oriented programming and relational databases by having an in-memory representation of the conceptual model (the objects), the storage schema (the database) and a mapping between the two.</span></span> <span data-ttu-id="820d5-1007">這個中繼資料稱為「實體資料模型」或簡稱「EDM」。</span><span class="sxs-lookup"><span data-stu-id="820d5-1007">This metadata is called an Entity Data Model, or EDM for short.</span></span> <span data-ttu-id="820d5-1008">在此 EDM 中，Entity Framework 會衍生視圖，將記憶體中物件的資料來回往返資料庫。</span><span class="sxs-lookup"><span data-stu-id="820d5-1008">From this EDM, Entity Framework will derive the views to roundtrip data from the objects in memory to the database and back.</span></span>

<span data-ttu-id="820d5-1009">當 Entity Framework 與正式指定概念模型、儲存架構和對應的 EDMX 檔案搭配使用時，模型載入階段只需要驗證 EDM 是否正確（例如，確定沒有遺漏對應），然後產生視圖，然後驗證視圖並備妥此中繼資料以供使用。</span><span class="sxs-lookup"><span data-stu-id="820d5-1009">When Entity Framework is used with an EDMX file that formally specifies the conceptual model, the storage schema, and the mapping, then the model loading stage only has to validate that the EDM is correct (for example, make sure that no mappings are missing), then generate the views, then validate the views and have this metadata ready for use.</span></span> <span data-ttu-id="820d5-1010">只有然後才可以執行查詢，或將新資料儲存到資料存放區。</span><span class="sxs-lookup"><span data-stu-id="820d5-1010">Only then can a query be executed or new data be saved to the data store.</span></span>

<span data-ttu-id="820d5-1011">Code First 方法是一種精密的實體資料模型產生器。</span><span class="sxs-lookup"><span data-stu-id="820d5-1011">The Code First approach is, at its heart, a sophisticated Entity Data Model generator.</span></span> <span data-ttu-id="820d5-1012">Entity Framework 必須從提供的程式碼產生 EDM;它會藉由分析包含在模型中的類別、套用慣例，以及透過流暢的 API 來設定模型，來完成此動作。</span><span class="sxs-lookup"><span data-stu-id="820d5-1012">The Entity Framework has to produce an EDM from the provided code; it does so by analyzing the classes involved in the model, applying conventions and configuring the model via the Fluent API.</span></span> <span data-ttu-id="820d5-1013">建立 EDM 之後，Entity Framework 基本上的行為會與專案中存在 EDMX 檔案的方式相同。</span><span class="sxs-lookup"><span data-stu-id="820d5-1013">After the EDM is built, the Entity Framework essentially behaves the same way as it would had an EDMX file been present in the project.</span></span> <span data-ttu-id="820d5-1014">因此，從 Code First 建立模型會增加額外的複雜性，而這會轉譯成較慢的 Entity Framework 啟動時間（與具有 EDMX 相比）。</span><span class="sxs-lookup"><span data-stu-id="820d5-1014">Thus, building the model from Code First adds extra complexity that translates into a slower startup time for the Entity Framework when compared to having an EDMX.</span></span> <span data-ttu-id="820d5-1015">成本完全取決於所建立之模型的大小和複雜度。</span><span class="sxs-lookup"><span data-stu-id="820d5-1015">The cost is completely dependent on the size and complexity of the model that’s being built.</span></span>

<span data-ttu-id="820d5-1016">選擇使用 EDMX 與 Code First 時，請務必知道 Code First 所引進的彈性，會增加第一次建立模型的成本。</span><span class="sxs-lookup"><span data-stu-id="820d5-1016">When choosing to use EDMX versus Code First, it’s important to know that the flexibility introduced by Code First increases the cost of building the model for the first time.</span></span> <span data-ttu-id="820d5-1017">如果您的應用程式可以承受此第一次負載的成本，通常 Code First 會是慣用的方式。</span><span class="sxs-lookup"><span data-stu-id="820d5-1017">If your application can withstand the cost of this first-time load then typically Code First will be the preferred way to go.</span></span>

## <a name="10-investigating-performance"></a><span data-ttu-id="820d5-1018">10調查效能</span><span class="sxs-lookup"><span data-stu-id="820d5-1018">10 Investigating Performance</span></span>

### <a name="101-using-the-visual-studio-profiler"></a><span data-ttu-id="820d5-1019">10.1 使用 Visual Studio Profiler</span><span class="sxs-lookup"><span data-stu-id="820d5-1019">10.1 Using the Visual Studio Profiler</span></span>

<span data-ttu-id="820d5-1020">如果您的 Entity Framework 發生效能問題，您可以使用類似 Visual Studio 內建的分析工具來查看應用程式花費時間的位置。</span><span class="sxs-lookup"><span data-stu-id="820d5-1020">If you are having performance issues with the Entity Framework, you can use a profiler like the one built into Visual Studio to see where your application is spending its time.</span></span> <span data-ttu-id="820d5-1021">這是我們用來在「探索 ADO.NET 的效能 Entity Framework-第1部」的 blog 文章（\<http://blogs.msdn.com/b/adonet/archive/2008/02/04/exploring-the-performance-of-the-ado-net-entity-framework-part-1.aspx>) 中用來產生圓形圖的工具，顯示 Entity Framework 在冷和暖查詢期間花費的時間。</span><span class="sxs-lookup"><span data-stu-id="820d5-1021">This is the tool we used to generate the pie charts in the “Exploring the Performance of the ADO.NET Entity Framework - Part 1” blog post ( \<http://blogs.msdn.com/b/adonet/archive/2008/02/04/exploring-the-performance-of-the-ado-net-entity-framework-part-1.aspx>) that show where Entity Framework spends its time during cold and warm queries.</span></span>

<span data-ttu-id="820d5-1022">「使用 Visual Studio 2010 Profiler 撰寫的分析 Entity Framework」的資料和模型化客戶諮詢小組會顯示如何使用 Profiler 來調查效能問題的真實世界範例。</span><span class="sxs-lookup"><span data-stu-id="820d5-1022">The "Profiling Entity Framework using the Visual Studio 2010 Profiler" blog post written by the Data and Modeling Customer Advisory Team shows a real-world example of how they used the profiler to investigate a performance problem.</span></span><span data-ttu-id="820d5-1023">  \<http://blogs.msdn.com/b/dmcat/archive/2010/04/30/profiling-entity-framework-using-the-visual-studio-2010-profiler.aspx>。</span><span class="sxs-lookup"><span data-stu-id="820d5-1023">  \<http://blogs.msdn.com/b/dmcat/archive/2010/04/30/profiling-entity-framework-using-the-visual-studio-2010-profiler.aspx>.</span></span> <span data-ttu-id="820d5-1024">這篇文章是針對 windows 應用程式所撰寫的。</span><span class="sxs-lookup"><span data-stu-id="820d5-1024">This post was written for a windows application.</span></span> <span data-ttu-id="820d5-1025">如果您需要分析 web 應用程式，Windows 效能記錄檔（WPR）和 Windows Performance Analyzer （WPA）工具的運作效果可能會比 Visual Studio 工作更好。</span><span class="sxs-lookup"><span data-stu-id="820d5-1025">If you need to profile a web application the Windows Performance Recorder (WPR) and Windows Performance Analyzer (WPA) tools may work better than working from Visual Studio.</span></span> <span data-ttu-id="820d5-1026">WPR 和 WPA 屬於 windows 評定及部署套件（ [http://www.microsoft.com/download/details.aspx?id=39982](https://www.microsoft.com/download/details.aspx?id=39982)）隨附的 Windows 效能工具組。</span><span class="sxs-lookup"><span data-stu-id="820d5-1026">WPR and WPA are part of the Windows Performance Toolkit which is included with the Windows Assessment and Deployment Kit ( [http://www.microsoft.com/download/details.aspx?id=39982](https://www.microsoft.com/download/details.aspx?id=39982)).</span></span>

### <a name="102-applicationdatabase-profiling"></a><span data-ttu-id="820d5-1027">10.2 應用程式/資料庫分析</span><span class="sxs-lookup"><span data-stu-id="820d5-1027">10.2 Application/Database profiling</span></span>

<span data-ttu-id="820d5-1028">像是內建在 Visual Studio 的工具，會告訴您應用程式花費時間的位置。</span><span class="sxs-lookup"><span data-stu-id="820d5-1028">Tools like the profiler built into Visual Studio tell you where your application is spending time.</span></span><span data-ttu-id="820d5-1029">  另一種分析工具可供使用，以根據需求在生產或預先生產環境中執行應用程式的動態分析，並尋找常見的錯誤和資料庫存取的反模式。</span><span class="sxs-lookup"><span data-stu-id="820d5-1029">  Another type of profiler is available that performs dynamic analysis of your running application, either in production or pre-production depending on needs, and looks for common pitfalls and anti-patterns of database access.</span></span>

<span data-ttu-id="820d5-1030">Entity Framework Profiler （\<http://efprof.com>) 和 ORMProfiler （\<http://ormprofiler.com>)）是兩個商用的分析工具。</span><span class="sxs-lookup"><span data-stu-id="820d5-1030">Two commercially available profilers are the Entity Framework Profiler ( \<http://efprof.com>) and ORMProfiler ( \<http://ormprofiler.com>).</span></span>

<span data-ttu-id="820d5-1031">如果您的應用程式是使用 Code First 的 MVC 應用程式，您可以使用 Stackexchange.redis 的 Miniprofiler 撼動。</span><span class="sxs-lookup"><span data-stu-id="820d5-1031">If your application is an MVC application using Code First, you can use StackExchange's MiniProfiler.</span></span> <span data-ttu-id="820d5-1032">Scott Hanselman 會在他的 blog 中描述這項工具，網址為： \<http://www.hanselman.com/blog/NuGetPackageOfTheWeek9ASPNETMiniProfilerFromStackExchangeRocksYourWorld.aspx>。</span><span class="sxs-lookup"><span data-stu-id="820d5-1032">Scott Hanselman describes this tool in his blog at: \<http://www.hanselman.com/blog/NuGetPackageOfTheWeek9ASPNETMiniProfilerFromStackExchangeRocksYourWorld.aspx>.</span></span>

<span data-ttu-id="820d5-1033">如需分析應用程式資料庫活動的詳細資訊，請參閱 Julie Lerman 的 MSDN 雜誌文章，其標題為[Entity Framework 中的分析資料庫活動](https://msdn.microsoft.com/magazine/gg490349.aspx)。</span><span class="sxs-lookup"><span data-stu-id="820d5-1033">For more information on profiling your application's database activity, see Julie Lerman's MSDN Magazine article titled [Profiling Database Activity in the Entity Framework](https://msdn.microsoft.com/magazine/gg490349.aspx).</span></span>

### <a name="103-database-logger"></a><span data-ttu-id="820d5-1034">10.3 資料庫記錄器</span><span class="sxs-lookup"><span data-stu-id="820d5-1034">10.3 Database logger</span></span>

<span data-ttu-id="820d5-1035">如果您使用 Entity Framework 6 也請考慮使用內建的記錄功能。</span><span class="sxs-lookup"><span data-stu-id="820d5-1035">If you are using Entity Framework 6 also consider using the built-in logging functionality.</span></span> <span data-ttu-id="820d5-1036">您可以透過簡單的單行設定，指示內容的 Database 屬性來記錄其活動：</span><span class="sxs-lookup"><span data-stu-id="820d5-1036">The Database property of the context can be instructed to log its activity via a simple one-line configuration:</span></span>

``` csharp
    using (var context = newQueryComparison.DbC.NorthwindEntities())
    {
        context.Database.Log = Console.WriteLine;
        var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
        q.ToList();
    }
```

<span data-ttu-id="820d5-1037">在此範例中，資料庫活動會記錄到主控台，但是可以設定記錄檔屬性，以呼叫&lt;字串&gt; 委派的任何動作。</span><span class="sxs-lookup"><span data-stu-id="820d5-1037">In this example the database activity will be logged to the console, but the Log property can be configured to call any Action&lt;string&gt; delegate.</span></span>

<span data-ttu-id="820d5-1038">如果您想要啟用資料庫記錄而不重新編譯，而且您使用 Entity Framework 6.1 或更新版本，您可以在應用程式的 web.config 或 app.config 檔案中新增攔截器來執行此動作。</span><span class="sxs-lookup"><span data-stu-id="820d5-1038">If you want to enable database logging without recompiling, and you are using Entity Framework 6.1 or later, you can do so by adding an interceptor in the web.config or app.config file of your application.</span></span>

``` xml
  <interceptors>
    <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework">
      <parameters>
        <parameter value="C:\Path\To\My\LogOutput.txt"/>
      </parameters>
    </interceptor>
  </interceptors>
```

<span data-ttu-id="820d5-1039">如需如何在不重新編譯的情況下新增記錄的詳細資訊，請移至 \<http://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/>。</span><span class="sxs-lookup"><span data-stu-id="820d5-1039">For more information on how to add logging without recompiling go to \<http://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/>.</span></span>

## <a name="11-appendix"></a><span data-ttu-id="820d5-1040">11附錄</span><span class="sxs-lookup"><span data-stu-id="820d5-1040">11 Appendix</span></span>

### <a name="111-a-test-environment"></a><span data-ttu-id="820d5-1041">11.1 A. 測試環境</span><span class="sxs-lookup"><span data-stu-id="820d5-1041">11.1 A. Test Environment</span></span>

<span data-ttu-id="820d5-1042">此環境會在與用戶端應用程式不同的電腦上，使用具有資料庫的2部電腦設定。</span><span class="sxs-lookup"><span data-stu-id="820d5-1042">This environment uses a 2-machine setup with the database on a separate machine from the client application.</span></span> <span data-ttu-id="820d5-1043">機器位於相同的機架中，因此網路延遲相對較低，但比單一電腦環境更實際。</span><span class="sxs-lookup"><span data-stu-id="820d5-1043">Machines are in the same rack, so network latency is relatively low, but more realistic than a single-machine environment.</span></span>

#### <a name="1111-app-server"></a><span data-ttu-id="820d5-1044">11.1.1 應用程式伺服器</span><span class="sxs-lookup"><span data-stu-id="820d5-1044">11.1.1       App Server</span></span>

##### <a name="11111-software-environment"></a><span data-ttu-id="820d5-1045">11.1.1.1 軟體環境</span><span class="sxs-lookup"><span data-stu-id="820d5-1045">11.1.1.1      Software Environment</span></span>

-   <span data-ttu-id="820d5-1046">Entity Framework 4 軟體環境</span><span class="sxs-lookup"><span data-stu-id="820d5-1046">Entity Framework 4 Software Environment</span></span>
    -   <span data-ttu-id="820d5-1047">OS 名稱： Windows Server 2008 R2 Enterprise SP1。</span><span class="sxs-lookup"><span data-stu-id="820d5-1047">OS Name: Windows Server 2008 R2 Enterprise SP1.</span></span>
    -   <span data-ttu-id="820d5-1048">Visual Studio 2010 –旗艦版。</span><span class="sxs-lookup"><span data-stu-id="820d5-1048">Visual Studio 2010 – Ultimate.</span></span>
    -   <span data-ttu-id="820d5-1049">Visual Studio 2010 SP1 （僅適用于某些比較）。</span><span class="sxs-lookup"><span data-stu-id="820d5-1049">Visual Studio 2010 SP1 (only for some comparisons).</span></span>
-   <span data-ttu-id="820d5-1050">Entity Framework 5 和6軟體環境</span><span class="sxs-lookup"><span data-stu-id="820d5-1050">Entity Framework 5 and 6 Software Environment</span></span>
    -   <span data-ttu-id="820d5-1051">OS 名稱： Windows 8.1 Enterprise</span><span class="sxs-lookup"><span data-stu-id="820d5-1051">OS Name: Windows 8.1 Enterprise</span></span>
    -   <span data-ttu-id="820d5-1052">Visual Studio 2013 –旗艦版。</span><span class="sxs-lookup"><span data-stu-id="820d5-1052">Visual Studio 2013 – Ultimate.</span></span>

##### <a name="11112-hardware-environment"></a><span data-ttu-id="820d5-1053">11.1.1.2 硬體環境</span><span class="sxs-lookup"><span data-stu-id="820d5-1053">11.1.1.2      Hardware Environment</span></span>

-   <span data-ttu-id="820d5-1054">雙處理器： Intel （R）2.27 （R） CPU L5520 W3530 @ g h z，2261 Mhz8 GHz，4核心，84個邏輯處理器（秒）。</span><span class="sxs-lookup"><span data-stu-id="820d5-1054">Dual Processor:     Intel(R) Xeon(R) CPU L5520 W3530 @ 2.27GHz, 2261 Mhz8 GHz, 4 Core(s), 84 Logical Processor(s).</span></span>
-   <span data-ttu-id="820d5-1055">2412 GB RamRAM。</span><span class="sxs-lookup"><span data-stu-id="820d5-1055">2412 GB RamRAM.</span></span>
-   <span data-ttu-id="820d5-1056">136 GB SCSI250GB SATA 7200 rpm 3GB/s 磁片磁碟機分割成4個磁碟分割。</span><span class="sxs-lookup"><span data-stu-id="820d5-1056">136 GB SCSI250GB SATA 7200 rpm 3GB/s drive split into 4 partitions.</span></span>

#### <a name="1112-db-server"></a><span data-ttu-id="820d5-1057">11.1.2 DB 伺服器</span><span class="sxs-lookup"><span data-stu-id="820d5-1057">11.1.2       DB server</span></span>

##### <a name="11121-software-environment"></a><span data-ttu-id="820d5-1058">11.1.2.1 軟體環境</span><span class="sxs-lookup"><span data-stu-id="820d5-1058">11.1.2.1      Software Environment</span></span>

-   <span data-ttu-id="820d5-1059">OS 名稱： Windows Server 2008 R 28.1 Enterprise SP1。</span><span class="sxs-lookup"><span data-stu-id="820d5-1059">OS Name: Windows Server 2008 R28.1 Enterprise SP1.</span></span>
-   <span data-ttu-id="820d5-1060">SQL Server 2008 R22012。</span><span class="sxs-lookup"><span data-stu-id="820d5-1060">SQL Server 2008 R22012.</span></span>

##### <a name="11122-hardware-environment"></a><span data-ttu-id="820d5-1061">11.1.2.2 硬體環境</span><span class="sxs-lookup"><span data-stu-id="820d5-1061">11.1.2.2      Hardware Environment</span></span>

-   <span data-ttu-id="820d5-1062">單處理器： Intel （R）第4方（R） CPU L5520 @ 2.27 GHz，2261 MhzES-1620 0 @ 3.60 GHz，4核心，8個邏輯處理器。</span><span class="sxs-lookup"><span data-stu-id="820d5-1062">Single Processor: Intel(R) Xeon(R) CPU L5520  @ 2.27GHz, 2261 MhzES-1620 0 @ 3.60GHz, 4 Core(s), 8 Logical Processor(s).</span></span>
-   <span data-ttu-id="820d5-1063">824 GB RamRAM。</span><span class="sxs-lookup"><span data-stu-id="820d5-1063">824 GB RamRAM.</span></span>
-   <span data-ttu-id="820d5-1064">465 GB ATA500GB SATA 7200 rpm 6GB/s 磁片磁碟機分割成4個磁碟分割。</span><span class="sxs-lookup"><span data-stu-id="820d5-1064">465 GB ATA500GB SATA 7200 rpm 6GB/s drive split into 4 partitions.</span></span>

### <a name="112-b-query-performance-comparison-tests"></a><span data-ttu-id="820d5-1065">11.2 b. 查詢效能比較測試</span><span class="sxs-lookup"><span data-stu-id="820d5-1065">11.2      B. Query performance comparison tests</span></span>

<span data-ttu-id="820d5-1066">Northwind 模型是用來執行這些測試。</span><span class="sxs-lookup"><span data-stu-id="820d5-1066">The Northwind model was used to execute these tests.</span></span> <span data-ttu-id="820d5-1067">它是使用 Entity Framework 設計工具從資料庫產生的。</span><span class="sxs-lookup"><span data-stu-id="820d5-1067">It was generated from the database using the Entity Framework designer.</span></span> <span data-ttu-id="820d5-1068">然後，使用下列程式碼來比較查詢執行選項的效能：</span><span class="sxs-lookup"><span data-stu-id="820d5-1068">Then, the following code was used to compare the performance of the query execution options:</span></span>

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

### <a name="113-c-navision-model"></a><span data-ttu-id="820d5-1069">11.3 c. Navision 模型</span><span class="sxs-lookup"><span data-stu-id="820d5-1069">11.3 C. Navision Model</span></span>

<span data-ttu-id="820d5-1070">Navision 資料庫是用來示範 Microsoft Dynamics – NAV 的大型資料庫。</span><span class="sxs-lookup"><span data-stu-id="820d5-1070">The Navision database is a large database used to demo Microsoft Dynamics – NAV.</span></span> <span data-ttu-id="820d5-1071">產生的概念模型包含1005個實體集和4227個關聯集。</span><span class="sxs-lookup"><span data-stu-id="820d5-1071">The generated conceptual model contains 1005 entity sets and 4227 association sets.</span></span> <span data-ttu-id="820d5-1072">測試中使用的模型是「平面」–尚未新增任何繼承。</span><span class="sxs-lookup"><span data-stu-id="820d5-1072">The model used in the test is “flat” – no inheritance has been added to it.</span></span>

#### <a name="1131-queries-used-for-navision-tests"></a><span data-ttu-id="820d5-1073">用於 Navision 測試的11.3.1 查詢</span><span class="sxs-lookup"><span data-stu-id="820d5-1073">11.3.1 Queries used for Navision tests</span></span>

<span data-ttu-id="820d5-1074">與 Navision 模型搭配使用的查詢清單包含3個類別的 Entity SQL 查詢：</span><span class="sxs-lookup"><span data-stu-id="820d5-1074">The queries list used with the Navision model contains 3 categories of Entity SQL queries:</span></span>

##### <a name="11311-lookup"></a><span data-ttu-id="820d5-1075">11.3.1.1 查閱</span><span class="sxs-lookup"><span data-stu-id="820d5-1075">11.3.1.1 Lookup</span></span>

<span data-ttu-id="820d5-1076">不含匯總的簡單查閱查詢</span><span class="sxs-lookup"><span data-stu-id="820d5-1076">A simple lookup query with no aggregations</span></span>

-   <span data-ttu-id="820d5-1077">計數：16232</span><span class="sxs-lookup"><span data-stu-id="820d5-1077">Count: 16232</span></span>
-   <span data-ttu-id="820d5-1078">範例：</span><span class="sxs-lookup"><span data-stu-id="820d5-1078">Example:</span></span>

``` xml
  <Query complexity="Lookup">
    <CommandText>Select value distinct top(4) e.Idle_Time From NavisionFKContext.Session as e</CommandText>
  </Query>
```

##### <a name="11312singleaggregating"></a><span data-ttu-id="820d5-1079">11.3.1.2 SingleAggregating</span><span class="sxs-lookup"><span data-stu-id="820d5-1079">11.3.1.2 SingleAggregating</span></span>

<span data-ttu-id="820d5-1080">具有多個匯總但沒有小計（單一查詢）的一般 BI 查詢</span><span class="sxs-lookup"><span data-stu-id="820d5-1080">A normal BI query with multiple aggregations, but no subtotals (single query)</span></span>

-   <span data-ttu-id="820d5-1081">計數：2313</span><span class="sxs-lookup"><span data-stu-id="820d5-1081">Count: 2313</span></span>
-   <span data-ttu-id="820d5-1082">範例：</span><span class="sxs-lookup"><span data-stu-id="820d5-1082">Example:</span></span>

``` xml
  <Query complexity="SingleAggregating">
    <CommandText>NavisionFK.MDF_SessionLogin_Time_Max()</CommandText>
  </Query>
```

<span data-ttu-id="820d5-1083">其中 MDF\_SessionLogin\_Time\_Max （）在模型中定義為：</span><span class="sxs-lookup"><span data-stu-id="820d5-1083">Where MDF\_SessionLogin\_Time\_Max() is defined in the model as:</span></span>

``` xml
  <Function Name="MDF_SessionLogin_Time_Max" ReturnType="Collection(DateTime)">
    <DefiningExpression>SELECT VALUE Edm.Min(E.Login_Time) FROM NavisionFKContext.Session as E</DefiningExpression>
  </Function>
```

##### <a name="11313aggregatingsubtotals"></a><span data-ttu-id="820d5-1084">11.3.1.3 AggregatingSubtotals</span><span class="sxs-lookup"><span data-stu-id="820d5-1084">11.3.1.3 AggregatingSubtotals</span></span>

<span data-ttu-id="820d5-1085">具有匯總和小計的 BI 查詢（透過 union all）</span><span class="sxs-lookup"><span data-stu-id="820d5-1085">A BI query with aggregations and subtotals (via union all)</span></span>

-   <span data-ttu-id="820d5-1086">計數：178</span><span class="sxs-lookup"><span data-stu-id="820d5-1086">Count: 178</span></span>
-   <span data-ttu-id="820d5-1087">範例：</span><span class="sxs-lookup"><span data-stu-id="820d5-1087">Example:</span></span>

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
