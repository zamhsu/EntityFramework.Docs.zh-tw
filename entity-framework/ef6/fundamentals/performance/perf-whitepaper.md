---
title: EF4、EF5 和 EF6 的性能注意事項 - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: d6d5a465-6434-45fa-855d-5eb48c61a2ea
ms.openlocfilehash: 0ece383bb5083b41c7a2636c009473333af6d3e2
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434335"
---
# <a name="performance-considerations-for-ef-4-5-and-6"></a><span data-ttu-id="b9641-102">EF 4、5 和 6 的性能注意事項</span><span class="sxs-lookup"><span data-stu-id="b9641-102">Performance considerations for EF 4, 5, and 6</span></span>
<span data-ttu-id="b9641-103">由大衛·奧班多,埃裡克·丁格和其他人</span><span class="sxs-lookup"><span data-stu-id="b9641-103">By David Obando, Eric Dettinger and others</span></span>

<span data-ttu-id="b9641-104">發佈時間:2012 年 4 月</span><span class="sxs-lookup"><span data-stu-id="b9641-104">Published: April 2012</span></span>

<span data-ttu-id="b9641-105">上次更新時間:2014 年 5 月</span><span class="sxs-lookup"><span data-stu-id="b9641-105">Last updated: May 2014</span></span>

------------------------------------------------------------------------

## <a name="1-introduction"></a><span data-ttu-id="b9641-106">1. 簡介</span><span class="sxs-lookup"><span data-stu-id="b9641-106">1. Introduction</span></span>

<span data-ttu-id="b9641-107">對象關係映射框架是一種為面向物件的應用程序中的數據訪問提供抽象的便捷方法。</span><span class="sxs-lookup"><span data-stu-id="b9641-107">Object-Relational Mapping frameworks are a convenient way to provide an abstraction for data access in an object-oriented application.</span></span> <span data-ttu-id="b9641-108">對於 .NET 應用程式,Microsoft 推薦的 O/RM 是實體框架。</span><span class="sxs-lookup"><span data-stu-id="b9641-108">For .NET applications, Microsoft's recommended O/RM is Entity Framework.</span></span> <span data-ttu-id="b9641-109">然而,對於任何抽象,性能都可能成為一個問題。</span><span class="sxs-lookup"><span data-stu-id="b9641-109">With any abstraction though, performance can become a concern.</span></span>

<span data-ttu-id="b9641-110">編寫本白皮書是為了顯示使用實體框架開發應用程式時的性能注意事項,讓開發人員瞭解可能影響性能的實體框架內部演演演算法,並提供調查和改進使用實體框架的應用程式的性能的提示。</span><span class="sxs-lookup"><span data-stu-id="b9641-110">This whitepaper was written to show the performance considerations when developing applications using Entity Framework, to give developers an idea of the Entity Framework internal algorithms that can affect performance, and to provide tips for investigation and improving performance in their applications that use Entity Framework.</span></span> <span data-ttu-id="b9641-111">Web 上已有許多有關性能的好主題,我們還嘗試盡可能指向這些資源。</span><span class="sxs-lookup"><span data-stu-id="b9641-111">There are a number of good topics on performance already available on the web, and we've also tried pointing to these resources where possible.</span></span>

<span data-ttu-id="b9641-112">性能是一個棘手的話題。</span><span class="sxs-lookup"><span data-stu-id="b9641-112">Performance is a tricky topic.</span></span> <span data-ttu-id="b9641-113">本白皮書旨在作為資源,説明您為使用實體框架的應用程式做出與性能相關的決策。</span><span class="sxs-lookup"><span data-stu-id="b9641-113">This whitepaper is intended as a resource to help you make performance related decisions for your applications that use Entity Framework.</span></span> <span data-ttu-id="b9641-114">我們包括一些測試指標來演示性能,但這些指標並非旨在作為您在應用程式中看到的性能的絕對指標。</span><span class="sxs-lookup"><span data-stu-id="b9641-114">We have included some test metrics to demonstrate performance, but these metrics aren't intended as absolute indicators of the performance you will see in your application.</span></span>

<span data-ttu-id="b9641-115">出於實際目的,本文檔假定實體框架 4 在 .NET 4.0 下運行,實體框架 5 和 6 在 .NET 4.5 下運行。</span><span class="sxs-lookup"><span data-stu-id="b9641-115">For practical purposes, this document assumes Entity Framework 4 is run under .NET 4.0 and Entity Framework 5 and 6 are run under .NET 4.5.</span></span> <span data-ttu-id="b9641-116">實體框架 5 的許多性能改進都位於提供 .NET 4.5 的核心元件中。</span><span class="sxs-lookup"><span data-stu-id="b9641-116">Many of the performance improvements made for Entity Framework 5 reside within the core components that ship with .NET 4.5.</span></span>

<span data-ttu-id="b9641-117">實體框架 6 是帶外版本,不依賴於隨 .NET 一起發貨的實體框架元件。</span><span class="sxs-lookup"><span data-stu-id="b9641-117">Entity Framework 6 is an out of band release and does not depend on the Entity Framework components that ship with .NET.</span></span> <span data-ttu-id="b9641-118">實體框架 6 在 .NET 4.0 和 .NET 4.5 上都工作,可以為尚未從 .NET 4.0 升級但希望在其應用程式中使用最新實體框架位的使用者提供巨大的性能優勢。</span><span class="sxs-lookup"><span data-stu-id="b9641-118">Entity Framework 6 work on both .NET 4.0 and .NET 4.5, and can offer a big performance benefit to those who haven’t upgraded from .NET 4.0 but want the latest Entity Framework bits in their application.</span></span> <span data-ttu-id="b9641-119">當本文檔提到實體框架 6 時,它指的是本文編寫時可用的最新版本:版本 6.1.0。</span><span class="sxs-lookup"><span data-stu-id="b9641-119">When this document mentions Entity Framework 6, it refers to the latest version available at the time of this writing: version 6.1.0.</span></span>

## <a name="2-cold-vs-warm-query-execution"></a><span data-ttu-id="b9641-120">2. 冷與暖查詢執行</span><span class="sxs-lookup"><span data-stu-id="b9641-120">2. Cold vs. Warm Query Execution</span></span>

<span data-ttu-id="b9641-121">首次對給定模型進行任何查詢時,實體框架會在幕後執行大量工作來載入和驗證模型。</span><span class="sxs-lookup"><span data-stu-id="b9641-121">The very first time any query is made against a given model, the Entity Framework does a lot of work behind the scenes to load and validate the model.</span></span> <span data-ttu-id="b9641-122">我們經常將第一個查詢稱為"冷"查詢。</span><span class="sxs-lookup"><span data-stu-id="b9641-122">We frequently refer to this first query as a "cold" query.</span></span><span data-ttu-id="b9641-123">針對已載入模型的進一步查詢稱為「暖」查詢,並且速度更快。</span><span class="sxs-lookup"><span data-stu-id="b9641-123">  Further queries against an already loaded model are known as "warm" queries, and are much faster.</span></span>

<span data-ttu-id="b9641-124">讓我們從高級檢視來瞭解使用實體框架執行查詢時所花費的時間,並瞭解實體框架 6 中情況正在改善。</span><span class="sxs-lookup"><span data-stu-id="b9641-124">Let’s take a high-level view of where time is spent when executing a query using Entity Framework, and see where things are improving in Entity Framework 6.</span></span>

<span data-ttu-id="b9641-125">**第一次查詢執行 = 冷查詢**</span><span class="sxs-lookup"><span data-stu-id="b9641-125">**First Query Execution – cold query**</span></span>

| <span data-ttu-id="b9641-126">代碼使用者寫入</span><span class="sxs-lookup"><span data-stu-id="b9641-126">Code User Writes</span></span>                                                                                     | <span data-ttu-id="b9641-127">動作</span><span class="sxs-lookup"><span data-stu-id="b9641-127">Action</span></span>                    | <span data-ttu-id="b9641-128">EF4 效能影響</span><span class="sxs-lookup"><span data-stu-id="b9641-128">EF4 Performance Impact</span></span>                                                                                                                                                                                                                                                                                                                                                                                                        | <span data-ttu-id="b9641-129">EF5 效能影響</span><span class="sxs-lookup"><span data-stu-id="b9641-129">EF5 Performance Impact</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                    | <span data-ttu-id="b9641-130">EF6 效能影響</span><span class="sxs-lookup"><span data-stu-id="b9641-130">EF6 Performance Impact</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
|:-----------------------------------------------------------------------------------------------------|:--------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `using(var db = new MyContext())` <br/> `{`                                                          | <span data-ttu-id="b9641-131">內容建立</span><span class="sxs-lookup"><span data-stu-id="b9641-131">Context creation</span></span>          | <span data-ttu-id="b9641-132">中</span><span class="sxs-lookup"><span data-stu-id="b9641-132">Medium</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                        | <span data-ttu-id="b9641-133">中</span><span class="sxs-lookup"><span data-stu-id="b9641-133">Medium</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | <span data-ttu-id="b9641-134">低</span><span class="sxs-lookup"><span data-stu-id="b9641-134">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `  var q1 = ` <br/> `    from c in db.Customers` <br/> `    where c.Id == id1` <br/> `    select c;` | <span data-ttu-id="b9641-135">查詢運算式建立</span><span class="sxs-lookup"><span data-stu-id="b9641-135">Query expression creation</span></span> | <span data-ttu-id="b9641-136">低</span><span class="sxs-lookup"><span data-stu-id="b9641-136">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                           | <span data-ttu-id="b9641-137">低</span><span class="sxs-lookup"><span data-stu-id="b9641-137">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="b9641-138">低</span><span class="sxs-lookup"><span data-stu-id="b9641-138">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `  var c1 = q1.First();`                                                                             | <span data-ttu-id="b9641-139">LINQ 查詢執行</span><span class="sxs-lookup"><span data-stu-id="b9641-139">LINQ query execution</span></span>      | <span data-ttu-id="b9641-140">- 中繼資料載入:高但快取</span><span class="sxs-lookup"><span data-stu-id="b9641-140">- Metadata loading: High but cached</span></span> <br/> <span data-ttu-id="b9641-141">- 檢視產生:可能非常高但快取</span><span class="sxs-lookup"><span data-stu-id="b9641-141">- View generation: Potentially very high but cached</span></span> <br/> <span data-ttu-id="b9641-142">- 參數評估:中等</span><span class="sxs-lookup"><span data-stu-id="b9641-142">- Parameter evaluation: Medium</span></span> <br/> <span data-ttu-id="b9641-143">- 查詢翻譯:中等</span><span class="sxs-lookup"><span data-stu-id="b9641-143">- Query translation: Medium</span></span> <br/> <span data-ttu-id="b9641-144">- 具體產生:中等但快取</span><span class="sxs-lookup"><span data-stu-id="b9641-144">- Materializer generation: Medium but cached</span></span> <br/> <span data-ttu-id="b9641-145">- 資料庫查詢執行:可能很高</span><span class="sxs-lookup"><span data-stu-id="b9641-145">- Database query execution: Potentially high</span></span> <br/> <span data-ttu-id="b9641-146">• 連接。開啟</span><span class="sxs-lookup"><span data-stu-id="b9641-146">+ Connection.Open</span></span> <br/> <span data-ttu-id="b9641-147">• 命令.執行讀取器</span><span class="sxs-lookup"><span data-stu-id="b9641-147">+ Command.ExecuteReader</span></span> <br/> <span data-ttu-id="b9641-148">• 資料閱讀器。閱讀</span><span class="sxs-lookup"><span data-stu-id="b9641-148">+ DataReader.Read</span></span> <br/> <span data-ttu-id="b9641-149">物件具體化:中等</span><span class="sxs-lookup"><span data-stu-id="b9641-149">Object materialization: Medium</span></span> <br/> <span data-ttu-id="b9641-150">- 身份尋找:中等</span><span class="sxs-lookup"><span data-stu-id="b9641-150">- Identity lookup: Medium</span></span> | <span data-ttu-id="b9641-151">- 中繼資料載入:高但快取</span><span class="sxs-lookup"><span data-stu-id="b9641-151">- Metadata loading: High but cached</span></span> <br/> <span data-ttu-id="b9641-152">- 檢視產生:可能非常高但快取</span><span class="sxs-lookup"><span data-stu-id="b9641-152">- View generation: Potentially very high but cached</span></span> <br/> <span data-ttu-id="b9641-153">- 參數評估:低</span><span class="sxs-lookup"><span data-stu-id="b9641-153">- Parameter evaluation: Low</span></span> <br/> <span data-ttu-id="b9641-154">- 查詢翻譯:中等但快取</span><span class="sxs-lookup"><span data-stu-id="b9641-154">- Query translation: Medium but cached</span></span> <br/> <span data-ttu-id="b9641-155">- 具體產生:中等但快取</span><span class="sxs-lookup"><span data-stu-id="b9641-155">- Materializer generation: Medium but cached</span></span> <br/> <span data-ttu-id="b9641-156">- 資料庫查詢執行:可能很高(在某些情況下查詢更好)</span><span class="sxs-lookup"><span data-stu-id="b9641-156">- Database query execution: Potentially high (Better queries in some situations)</span></span> <br/> <span data-ttu-id="b9641-157">• 連接。開啟</span><span class="sxs-lookup"><span data-stu-id="b9641-157">+ Connection.Open</span></span> <br/> <span data-ttu-id="b9641-158">• 命令.執行讀取器</span><span class="sxs-lookup"><span data-stu-id="b9641-158">+ Command.ExecuteReader</span></span> <br/> <span data-ttu-id="b9641-159">• 資料閱讀器。閱讀</span><span class="sxs-lookup"><span data-stu-id="b9641-159">+ DataReader.Read</span></span> <br/> <span data-ttu-id="b9641-160">物件具體化:中等</span><span class="sxs-lookup"><span data-stu-id="b9641-160">Object materialization: Medium</span></span> <br/> <span data-ttu-id="b9641-161">- 身份尋找:中等</span><span class="sxs-lookup"><span data-stu-id="b9641-161">- Identity lookup: Medium</span></span> | <span data-ttu-id="b9641-162">- 中繼資料載入:高但快取</span><span class="sxs-lookup"><span data-stu-id="b9641-162">- Metadata loading: High but cached</span></span> <br/> <span data-ttu-id="b9641-163">- 檢視產生:中等但快取</span><span class="sxs-lookup"><span data-stu-id="b9641-163">- View generation: Medium but cached</span></span> <br/> <span data-ttu-id="b9641-164">- 參數評估:低</span><span class="sxs-lookup"><span data-stu-id="b9641-164">- Parameter evaluation: Low</span></span> <br/> <span data-ttu-id="b9641-165">- 查詢翻譯:中等但快取</span><span class="sxs-lookup"><span data-stu-id="b9641-165">- Query translation: Medium but cached</span></span> <br/> <span data-ttu-id="b9641-166">- 具體產生:中等但快取</span><span class="sxs-lookup"><span data-stu-id="b9641-166">- Materializer generation: Medium but cached</span></span> <br/> <span data-ttu-id="b9641-167">- 資料庫查詢執行:可能很高(在某些情況下查詢更好)</span><span class="sxs-lookup"><span data-stu-id="b9641-167">- Database query execution: Potentially high (Better queries in some situations)</span></span> <br/> <span data-ttu-id="b9641-168">• 連接。開啟</span><span class="sxs-lookup"><span data-stu-id="b9641-168">+ Connection.Open</span></span> <br/> <span data-ttu-id="b9641-169">• 命令.執行讀取器</span><span class="sxs-lookup"><span data-stu-id="b9641-169">+ Command.ExecuteReader</span></span> <br/> <span data-ttu-id="b9641-170">• 資料閱讀器。閱讀</span><span class="sxs-lookup"><span data-stu-id="b9641-170">+ DataReader.Read</span></span> <br/> <span data-ttu-id="b9641-171">物件具體化:中等(快於 EF5)</span><span class="sxs-lookup"><span data-stu-id="b9641-171">Object materialization: Medium (Faster than EF5)</span></span> <br/> <span data-ttu-id="b9641-172">- 身份尋找:中等</span><span class="sxs-lookup"><span data-stu-id="b9641-172">- Identity lookup: Medium</span></span> |
| `}`                                                                                                  | <span data-ttu-id="b9641-173">連線.關閉</span><span class="sxs-lookup"><span data-stu-id="b9641-173">Connection.Close</span></span>          | <span data-ttu-id="b9641-174">低</span><span class="sxs-lookup"><span data-stu-id="b9641-174">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                           | <span data-ttu-id="b9641-175">低</span><span class="sxs-lookup"><span data-stu-id="b9641-175">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="b9641-176">低</span><span class="sxs-lookup"><span data-stu-id="b9641-176">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |


<span data-ttu-id="b9641-177">**第二次查詢執行 = 暖查詢**</span><span class="sxs-lookup"><span data-stu-id="b9641-177">**Second Query Execution – warm query**</span></span>

| <span data-ttu-id="b9641-178">代碼使用者寫入</span><span class="sxs-lookup"><span data-stu-id="b9641-178">Code User Writes</span></span>                                                                                     | <span data-ttu-id="b9641-179">動作</span><span class="sxs-lookup"><span data-stu-id="b9641-179">Action</span></span>                    | <span data-ttu-id="b9641-180">EF4 效能影響</span><span class="sxs-lookup"><span data-stu-id="b9641-180">EF4 Performance Impact</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | <span data-ttu-id="b9641-181">EF5 效能影響</span><span class="sxs-lookup"><span data-stu-id="b9641-181">EF5 Performance Impact</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | <span data-ttu-id="b9641-182">EF6 效能影響</span><span class="sxs-lookup"><span data-stu-id="b9641-182">EF6 Performance Impact</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
|:-----------------------------------------------------------------------------------------------------|:--------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `using(var db = new MyContext())` <br/> `{`                                                          | <span data-ttu-id="b9641-183">內容建立</span><span class="sxs-lookup"><span data-stu-id="b9641-183">Context creation</span></span>          | <span data-ttu-id="b9641-184">中</span><span class="sxs-lookup"><span data-stu-id="b9641-184">Medium</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | <span data-ttu-id="b9641-185">中</span><span class="sxs-lookup"><span data-stu-id="b9641-185">Medium</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | <span data-ttu-id="b9641-186">低</span><span class="sxs-lookup"><span data-stu-id="b9641-186">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `  var q1 = ` <br/> `    from c in db.Customers` <br/> `    where c.Id == id1` <br/> `    select c;` | <span data-ttu-id="b9641-187">查詢運算式建立</span><span class="sxs-lookup"><span data-stu-id="b9641-187">Query expression creation</span></span> | <span data-ttu-id="b9641-188">低</span><span class="sxs-lookup"><span data-stu-id="b9641-188">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | <span data-ttu-id="b9641-189">低</span><span class="sxs-lookup"><span data-stu-id="b9641-189">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | <span data-ttu-id="b9641-190">低</span><span class="sxs-lookup"><span data-stu-id="b9641-190">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `  var c1 = q1.First();`                                                                             | <span data-ttu-id="b9641-191">LINQ 查詢執行</span><span class="sxs-lookup"><span data-stu-id="b9641-191">LINQ query execution</span></span>      | <span data-ttu-id="b9641-192">- 中~~繼資料~~~~High but cached~~</span><span class="sxs-lookup"><span data-stu-id="b9641-192">- Metadata ~~loading~~ lookup: ~~High but cached~~ Low</span></span> <br/> <span data-ttu-id="b9641-193">- 查看~~產生~~搜尋:~~可能非常高但快取~~低</span><span class="sxs-lookup"><span data-stu-id="b9641-193">- View ~~generation~~ lookup: ~~Potentially very high but cached~~ Low</span></span> <br/> <span data-ttu-id="b9641-194">- 參數評估:中等</span><span class="sxs-lookup"><span data-stu-id="b9641-194">- Parameter evaluation: Medium</span></span> <br/> <span data-ttu-id="b9641-195">- 查詢~~翻譯~~搜尋:中等</span><span class="sxs-lookup"><span data-stu-id="b9641-195">- Query ~~translation~~ lookup: Medium</span></span> <br/> <span data-ttu-id="b9641-196">- 具體~~產生搜尋~~:~~中等但快取~~低</span><span class="sxs-lookup"><span data-stu-id="b9641-196">- Materializer ~~generation~~ lookup: ~~Medium but cached~~ Low</span></span> <br/> <span data-ttu-id="b9641-197">- 資料庫查詢執行:可能很高</span><span class="sxs-lookup"><span data-stu-id="b9641-197">- Database query execution: Potentially high</span></span> <br/> <span data-ttu-id="b9641-198">• 連接。開啟</span><span class="sxs-lookup"><span data-stu-id="b9641-198">+ Connection.Open</span></span> <br/> <span data-ttu-id="b9641-199">• 命令.執行讀取器</span><span class="sxs-lookup"><span data-stu-id="b9641-199">+ Command.ExecuteReader</span></span> <br/> <span data-ttu-id="b9641-200">• 資料閱讀器。閱讀</span><span class="sxs-lookup"><span data-stu-id="b9641-200">+ DataReader.Read</span></span> <br/> <span data-ttu-id="b9641-201">物件具體化:中等</span><span class="sxs-lookup"><span data-stu-id="b9641-201">Object materialization: Medium</span></span> <br/> <span data-ttu-id="b9641-202">- 身份尋找:中等</span><span class="sxs-lookup"><span data-stu-id="b9641-202">- Identity lookup: Medium</span></span> | <span data-ttu-id="b9641-203">- 中~~繼資料~~~~High but cached~~</span><span class="sxs-lookup"><span data-stu-id="b9641-203">- Metadata ~~loading~~ lookup: ~~High but cached~~ Low</span></span> <br/> <span data-ttu-id="b9641-204">- 查看~~產生~~搜尋:~~可能非常高但快取~~低</span><span class="sxs-lookup"><span data-stu-id="b9641-204">- View ~~generation~~ lookup: ~~Potentially very high but cached~~ Low</span></span> <br/> <span data-ttu-id="b9641-205">- 參數評估:低</span><span class="sxs-lookup"><span data-stu-id="b9641-205">- Parameter evaluation: Low</span></span> <br/> <span data-ttu-id="b9641-206">- 查詢~~翻譯~~搜尋:~~中但快取~~低</span><span class="sxs-lookup"><span data-stu-id="b9641-206">- Query ~~translation~~ lookup: ~~Medium but cached~~ Low</span></span> <br/> <span data-ttu-id="b9641-207">- 具體~~產生搜尋~~:~~中等但快取~~低</span><span class="sxs-lookup"><span data-stu-id="b9641-207">- Materializer ~~generation~~ lookup: ~~Medium but cached~~ Low</span></span> <br/> <span data-ttu-id="b9641-208">- 資料庫查詢執行:可能很高(在某些情況下查詢更好)</span><span class="sxs-lookup"><span data-stu-id="b9641-208">- Database query execution: Potentially high (Better queries in some situations)</span></span> <br/> <span data-ttu-id="b9641-209">• 連接。開啟</span><span class="sxs-lookup"><span data-stu-id="b9641-209">+ Connection.Open</span></span> <br/> <span data-ttu-id="b9641-210">• 命令.執行讀取器</span><span class="sxs-lookup"><span data-stu-id="b9641-210">+ Command.ExecuteReader</span></span> <br/> <span data-ttu-id="b9641-211">• 資料閱讀器。閱讀</span><span class="sxs-lookup"><span data-stu-id="b9641-211">+ DataReader.Read</span></span> <br/> <span data-ttu-id="b9641-212">物件具體化:中等</span><span class="sxs-lookup"><span data-stu-id="b9641-212">Object materialization: Medium</span></span> <br/> <span data-ttu-id="b9641-213">- 身份尋找:中等</span><span class="sxs-lookup"><span data-stu-id="b9641-213">- Identity lookup: Medium</span></span> | <span data-ttu-id="b9641-214">- 中~~繼資料~~~~High but cached~~</span><span class="sxs-lookup"><span data-stu-id="b9641-214">- Metadata ~~loading~~ lookup: ~~High but cached~~ Low</span></span> <br/> <span data-ttu-id="b9641-215">- 查看~~產生~~搜尋:~~中等但快取~~低</span><span class="sxs-lookup"><span data-stu-id="b9641-215">- View ~~generation~~ lookup: ~~Medium but cached~~ Low</span></span> <br/> <span data-ttu-id="b9641-216">- 參數評估:低</span><span class="sxs-lookup"><span data-stu-id="b9641-216">- Parameter evaluation: Low</span></span> <br/> <span data-ttu-id="b9641-217">- 查詢~~翻譯~~搜尋:~~中但快取~~低</span><span class="sxs-lookup"><span data-stu-id="b9641-217">- Query ~~translation~~ lookup: ~~Medium but cached~~ Low</span></span> <br/> <span data-ttu-id="b9641-218">- 具體~~產生搜尋~~:~~中等但快取~~低</span><span class="sxs-lookup"><span data-stu-id="b9641-218">- Materializer ~~generation~~ lookup: ~~Medium but cached~~ Low</span></span> <br/> <span data-ttu-id="b9641-219">- 資料庫查詢執行:可能很高(在某些情況下查詢更好)</span><span class="sxs-lookup"><span data-stu-id="b9641-219">- Database query execution: Potentially high (Better queries in some situations)</span></span> <br/> <span data-ttu-id="b9641-220">• 連接。開啟</span><span class="sxs-lookup"><span data-stu-id="b9641-220">+ Connection.Open</span></span> <br/> <span data-ttu-id="b9641-221">• 命令.執行讀取器</span><span class="sxs-lookup"><span data-stu-id="b9641-221">+ Command.ExecuteReader</span></span> <br/> <span data-ttu-id="b9641-222">• 資料閱讀器。閱讀</span><span class="sxs-lookup"><span data-stu-id="b9641-222">+ DataReader.Read</span></span> <br/> <span data-ttu-id="b9641-223">物件具體化:中等(快於 EF5)</span><span class="sxs-lookup"><span data-stu-id="b9641-223">Object materialization: Medium (Faster than EF5)</span></span> <br/> <span data-ttu-id="b9641-224">- 身份尋找:中等</span><span class="sxs-lookup"><span data-stu-id="b9641-224">- Identity lookup: Medium</span></span> |
| `}`                                                                                                  | <span data-ttu-id="b9641-225">連線.關閉</span><span class="sxs-lookup"><span data-stu-id="b9641-225">Connection.Close</span></span>          | <span data-ttu-id="b9641-226">低</span><span class="sxs-lookup"><span data-stu-id="b9641-226">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | <span data-ttu-id="b9641-227">低</span><span class="sxs-lookup"><span data-stu-id="b9641-227">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | <span data-ttu-id="b9641-228">低</span><span class="sxs-lookup"><span data-stu-id="b9641-228">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |


<span data-ttu-id="b9641-229">有幾種方法可以降低冷查詢和暖查詢的性能成本,我們將在下一節中介紹這些查詢。</span><span class="sxs-lookup"><span data-stu-id="b9641-229">There are several ways to reduce the performance cost of both cold and warm queries, and we'll take a look at these in the following section.</span></span> <span data-ttu-id="b9641-230">具體來說,我們將考慮通過使用預生成的視圖來降低冷查詢中模型載入的成本,這將有助於緩解在生成視圖期間遇到的性能難題。</span><span class="sxs-lookup"><span data-stu-id="b9641-230">Specifically, we'll look at reducing the cost of model loading in cold queries by using pre-generated views, which should help alleviate performance pains experienced during view generation.</span></span> <span data-ttu-id="b9641-231">對於暖查詢,我們將介紹查詢計劃緩存、無跟蹤查詢和不同的查詢執行選項。</span><span class="sxs-lookup"><span data-stu-id="b9641-231">For warm queries, we'll cover query plan caching, no tracking queries, and different query execution options.</span></span>

### <a name="21-what-is-view-generation"></a><span data-ttu-id="b9641-232">2.1 什麼是視圖生成?</span><span class="sxs-lookup"><span data-stu-id="b9641-232">2.1 What is View Generation?</span></span>

<span data-ttu-id="b9641-233">為了瞭解什麼是視圖生成,我們必須首先了解什麼是"映射視圖"。</span><span class="sxs-lookup"><span data-stu-id="b9641-233">In order to understand what view generation is, we must first understand what “Mapping Views” are.</span></span> <span data-ttu-id="b9641-234">映射檢視是映射中為每個實體集和關聯指定的轉換的可執行表示形式。</span><span class="sxs-lookup"><span data-stu-id="b9641-234">Mapping Views are executable representations of the transformations specified in the mapping for each entity set and association.</span></span> <span data-ttu-id="b9641-235">在內部,這些映射檢視採用 CQT(規範查詢樹)的形狀。</span><span class="sxs-lookup"><span data-stu-id="b9641-235">Internally, these mapping views take the shape of CQTs (canonical query trees).</span></span> <span data-ttu-id="b9641-236">有兩種類型的映射檢視:</span><span class="sxs-lookup"><span data-stu-id="b9641-236">There are two types of mapping views:</span></span>

-   <span data-ttu-id="b9641-237">查詢檢視:這些檢視表示從資料庫架構到概念模型所需的轉換。</span><span class="sxs-lookup"><span data-stu-id="b9641-237">Query views: these represent the transformation necessary to go from the database schema to the conceptual model.</span></span>
-   <span data-ttu-id="b9641-238">更新檢視:這些檢視表示從概念模型到資料庫架構所需的轉換。</span><span class="sxs-lookup"><span data-stu-id="b9641-238">Update views: these represent the transformation necessary to go from the conceptual model to the database schema.</span></span>

<span data-ttu-id="b9641-239">請記住,概念模型可能以各種方式與資料庫架構不同。</span><span class="sxs-lookup"><span data-stu-id="b9641-239">Keep in mind that the conceptual model might differ from the database schema in various ways.</span></span> <span data-ttu-id="b9641-240">例如,一個表可用於存儲兩種不同實體類型的數據。</span><span class="sxs-lookup"><span data-stu-id="b9641-240">For example, one single table might be used to store the data for two different entity types.</span></span> <span data-ttu-id="b9641-241">繼承和非常規映射在映射視圖的複雜性中起著一定的作用。</span><span class="sxs-lookup"><span data-stu-id="b9641-241">Inheritance and non-trivial mappings play a role in the complexity of the mapping views.</span></span>

<span data-ttu-id="b9641-242">基於映射規範計算這些檢視的過程就是我們所說的檢視生成。</span><span class="sxs-lookup"><span data-stu-id="b9641-242">The process of computing these views based on the specification of the mapping is what we call view generation.</span></span> <span data-ttu-id="b9641-243">視圖生成可以在載入模型時動態進行,也可以在生成時使用「預生成的檢視」進行動態生成;後者以實體 SQL 語句的形式序列化\#到 C 或 VB 檔。</span><span class="sxs-lookup"><span data-stu-id="b9641-243">View generation can either take place dynamically when a model is loaded, or at build time, by using "pre-generated views"; the latter are serialized in the form of Entity SQL statements to a C\# or VB file.</span></span>

<span data-ttu-id="b9641-244">生成視圖時,也會驗證視圖。</span><span class="sxs-lookup"><span data-stu-id="b9641-244">When views are generated, they are also validated.</span></span> <span data-ttu-id="b9641-245">從性能角度來看,視圖生成成本的絕大多數實際上是對視圖的驗證,這可確保實體之間的連接有意義,並且對所有支援的操作具有正確的基數。</span><span class="sxs-lookup"><span data-stu-id="b9641-245">From a performance standpoint, the vast majority of the cost of view generation is actually the validation of the views which ensures that the connections between the entities make sense and have the correct cardinality for all the supported operations.</span></span>

<span data-ttu-id="b9641-246">執行對實體集的查詢時,查詢將與相應的查詢檢視組合,並且此合成的結果通過計劃編譯器運行,以創建備份存儲可以理解的查詢的表示形式。</span><span class="sxs-lookup"><span data-stu-id="b9641-246">When a query over an entity set is executed, the query is combined with the corresponding query view, and the result of this composition is run through the plan compiler to create the representation of the query that the backing store can understand.</span></span> <span data-ttu-id="b9641-247">對於 SQL 伺服器,此編譯的最終結果將是 T-SQL SELECT 語句。</span><span class="sxs-lookup"><span data-stu-id="b9641-247">For SQL Server, the final result of this compilation will be a T-SQL SELECT statement.</span></span> <span data-ttu-id="b9641-248">首次對實體集執行更新時,更新視圖將通過類似的過程運行,以將其轉換為目標資料庫的 DML 語句。</span><span class="sxs-lookup"><span data-stu-id="b9641-248">The first time an update over an entity set is performed, the update view is run through a similar process to transform it into DML statements for the target database.</span></span>

### <a name="22-factors-that-affect-view-generation-performance"></a><span data-ttu-id="b9641-249">2.2 影響檢視產生性能的因素</span><span class="sxs-lookup"><span data-stu-id="b9641-249">2.2 Factors that affect View Generation performance</span></span>

<span data-ttu-id="b9641-250">視圖生成步驟的性能不僅取決於模型的大小,還取決於模型的互連程度。</span><span class="sxs-lookup"><span data-stu-id="b9641-250">The performance of view generation step not only depends on the size of your model but also on how interconnected the model is.</span></span> <span data-ttu-id="b9641-251">如果兩個實體通過繼承鏈或關聯連接,則它們表示已連接。</span><span class="sxs-lookup"><span data-stu-id="b9641-251">If two Entities are connected via an inheritance chain or an Association, they are said to be connected.</span></span> <span data-ttu-id="b9641-252">同樣,如果兩個表通過外鍵連接,則它們被連接。</span><span class="sxs-lookup"><span data-stu-id="b9641-252">Similarly if two tables are connected via a foreign key, they are connected.</span></span> <span data-ttu-id="b9641-253">隨著架構中連接的實體和表數量的增加,視圖生成成本也會增加。</span><span class="sxs-lookup"><span data-stu-id="b9641-253">As the number of connected Entities and tables in your schemas increase, the view generation cost increases.</span></span>

<span data-ttu-id="b9641-254">在最壞的情況下,我們用於生成和驗證視圖的演演演算法呈指數級,儘管我們使用一些優化來改進這一點。</span><span class="sxs-lookup"><span data-stu-id="b9641-254">The algorithm that we use to generate and validate views is exponential in the worst case, though we do use some optimizations to improve this.</span></span> <span data-ttu-id="b9641-255">似乎對性能產生負面影響的最大因素是:</span><span class="sxs-lookup"><span data-stu-id="b9641-255">The biggest factors that seem to negatively affect performance are:</span></span>

-   <span data-ttu-id="b9641-256">模型大小,指實體的數量和這些實體之間的關聯量。</span><span class="sxs-lookup"><span data-stu-id="b9641-256">Model size, referring to the number of entities and the amount of associations between these entities.</span></span>
-   <span data-ttu-id="b9641-257">模型複雜性,特別是涉及大量類型的繼承。</span><span class="sxs-lookup"><span data-stu-id="b9641-257">Model complexity, specifically inheritance involving a large number of types.</span></span>
-   <span data-ttu-id="b9641-258">使用獨立關聯,而不是外國金鑰關聯。</span><span class="sxs-lookup"><span data-stu-id="b9641-258">Using Independent Associations, instead of Foreign Key Associations.</span></span>

<span data-ttu-id="b9641-259">對於小型、簡單的模型,成本可能足夠小,無需使用預生成的視圖。</span><span class="sxs-lookup"><span data-stu-id="b9641-259">For small, simple models the cost may be small enough to not bother using pre-generated views.</span></span> <span data-ttu-id="b9641-260">隨著模型大小和複雜性的增加,有多種選項可用於降低檢視生成和驗證的成本。</span><span class="sxs-lookup"><span data-stu-id="b9641-260">As model size and complexity increase, there are several options available to reduce the cost of view generation and validation.</span></span>

### <a name="23-using-pre-generated-views-to-decrease-model-load-time"></a><span data-ttu-id="b9641-261">2.3 使用預生成的檢視減少模型載入時間</span><span class="sxs-lookup"><span data-stu-id="b9641-261">2.3 Using Pre-Generated Views to decrease model load time</span></span>

<span data-ttu-id="b9641-262">有關如何在實體框架 6 上使用預生成的檢視的詳細資訊,請造[訪 預生成的映射檢視](~/ef6/fundamentals/performance/pre-generated-views.md)</span><span class="sxs-lookup"><span data-stu-id="b9641-262">For detailed information on how to use pre-generated views on Entity Framework 6 visit [Pre-Generated Mapping Views](~/ef6/fundamentals/performance/pre-generated-views.md)</span></span>

#### <a name="231-pre-generated-views-using-the-entity-framework-power-tools-community-edition"></a><span data-ttu-id="b9641-263">2.3.1 使用實體框架電動工具社區版的預生成檢視</span><span class="sxs-lookup"><span data-stu-id="b9641-263">2.3.1 Pre-Generated views using the Entity Framework Power Tools Community Edition</span></span>

<span data-ttu-id="b9641-264">您可以使用[實體框架 6 電動工具社區版](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition)生成 EDMX 和代碼優先模型的檢視,通過右鍵單擊模型類檔並使用實體框架功能表選擇「生成檢視」。。</span><span class="sxs-lookup"><span data-stu-id="b9641-264">You can use the [Entity Framework 6 Power Tools Community Edition](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) to generate views of EDMX and Code First models by right-clicking the model class file and using the Entity Framework menu to select “Generate Views”.</span></span> <span data-ttu-id="b9641-265">實體框架電動工具社區版僅適用於 DbContext 派生的上下文。</span><span class="sxs-lookup"><span data-stu-id="b9641-265">The Entity Framework Power Tools Community Edition work only on DbContext-derived contexts.</span></span>

#### <a name="232-how-to-use-pre-generated-views-with-a-model-created-by-edmgen"></a><span data-ttu-id="b9641-266">2.3.2 如何使用預生成的檢視與 EDMGen 建立的模型</span><span class="sxs-lookup"><span data-stu-id="b9641-266">2.3.2 How to use Pre-generated views with a model created by EDMGen</span></span>

<span data-ttu-id="b9641-267">EDMGen 是一個實用程式,它與 .NET 一起提供,並與實體框架 4 和 5 一起工作,但與實體框架 6 無關。</span><span class="sxs-lookup"><span data-stu-id="b9641-267">EDMGen is a utility that ships with .NET and works with Entity Framework 4 and 5, but not with Entity Framework 6.</span></span> <span data-ttu-id="b9641-268">EDMGen 允許您從命令列生成模型檔、物件層和檢視。</span><span class="sxs-lookup"><span data-stu-id="b9641-268">EDMGen allows you to generate a model file, the object layer and the views from the command line.</span></span> <span data-ttu-id="b9641-269">其中一個輸出是您選擇的語言的「檢視」 檔,VB 或\#C 。</span><span class="sxs-lookup"><span data-stu-id="b9641-269">One of the outputs will be a Views file in your language of choice, VB or C\#.</span></span> <span data-ttu-id="b9641-270">這是一個代碼檔,其中包含每個實體集的實體 SQL 代碼段。</span><span class="sxs-lookup"><span data-stu-id="b9641-270">This is a code file containing Entity SQL snippets for each entity set.</span></span> <span data-ttu-id="b9641-271">要啟用預生成的檢視,只需將檔包含在專案中即可。</span><span class="sxs-lookup"><span data-stu-id="b9641-271">To enable pre-generated views, you simply include the file in your project.</span></span>

<span data-ttu-id="b9641-272">如果手動編輯模型的架構檔,則需要重新生成檢視檔。</span><span class="sxs-lookup"><span data-stu-id="b9641-272">If you manually make edits to the schema files for the model, you will need to re-generate the views file.</span></span> <span data-ttu-id="b9641-273">可以通過使用 **/mode:ViewGeneration**標誌執行 EDMGen 來執行此操作。</span><span class="sxs-lookup"><span data-stu-id="b9641-273">You can do this by running EDMGen with the **/mode:ViewGeneration** flag.</span></span>

#### <a name="233-how-to-use-pre-generated-views-with-an-edmx-file"></a><span data-ttu-id="b9641-274">2.3.3 如何使用預生成的檢視與 EDMX 檔案</span><span class="sxs-lookup"><span data-stu-id="b9641-274">2.3.3 How to use Pre-Generated Views with an EDMX file</span></span>

<span data-ttu-id="b9641-275">您還可以使用 EDMGen 為 EDMX 檔產生檢視 - 前面引用的 MSDN 主題介紹如何添加預建建事件來執行此操作 - 但這樣做很複雜,在某些情況下,這是不可能的。</span><span class="sxs-lookup"><span data-stu-id="b9641-275">You can also use EDMGen to generate views for an EDMX file - the previously referenced MSDN topic describes how to add a pre-build event to do this - but this is complicated and there are some cases where it isn't possible.</span></span> <span data-ttu-id="b9641-276">當模型位於 edmx 檔中時,通常使用 T4 範本生成檢視會更容易。</span><span class="sxs-lookup"><span data-stu-id="b9641-276">It's generally easier to use a T4 template to generate the views when your model is in an edmx file.</span></span>

<span data-ttu-id="b9641-277">ADO.NET團隊部落格有一篇文章,描述如何使用 T4 範本生成\<https://docs.microsoft.com/archive/blogs/adonet/how-to-use-a-t4-template-for-view-generation>)檢視 (。</span><span class="sxs-lookup"><span data-stu-id="b9641-277">The ADO.NET team blog has a post that describes how to use a T4 template for view generation ( \<https://docs.microsoft.com/archive/blogs/adonet/how-to-use-a-t4-template-for-view-generation>).</span></span> <span data-ttu-id="b9641-278">此帖子包含一個範本,可以下載並添加到您的專案中。</span><span class="sxs-lookup"><span data-stu-id="b9641-278">This post includes a template that can be downloaded and added to your project.</span></span> <span data-ttu-id="b9641-279">範本是為實體框架的第一個版本編寫的,因此不能保證它們能夠與最新版本的實體框架配合使用。</span><span class="sxs-lookup"><span data-stu-id="b9641-279">The template was written for the first version of Entity Framework, so they aren’t guaranteed to work with the latest versions of Entity Framework.</span></span> <span data-ttu-id="b9641-280">但是,可以從可視化工作室庫下載實體框架 4 和 5 的最新視圖生成範本集:</span><span class="sxs-lookup"><span data-stu-id="b9641-280">However, you can download a more up-to-date set of view generation templates for Entity Framework 4 and 5from the Visual Studio Gallery:</span></span>

-   <span data-ttu-id="b9641-281">VB.NET:\<http://visualstudiogallery.msdn.microsoft.com/118b44f2-1b91-4de2-a584-7a680418941d></span><span class="sxs-lookup"><span data-stu-id="b9641-281">VB.NET: \<http://visualstudiogallery.msdn.microsoft.com/118b44f2-1b91-4de2-a584-7a680418941d></span></span>
-   <span data-ttu-id="b9641-282">C\#:\<http://visualstudiogallery.msdn.microsoft.com/ae7730ce-ddab-470f-8456-1b313cd2c44d></span><span class="sxs-lookup"><span data-stu-id="b9641-282">C\#: \<http://visualstudiogallery.msdn.microsoft.com/ae7730ce-ddab-470f-8456-1b313cd2c44d></span></span>

<span data-ttu-id="b9641-283">如果您使用的是實體框架 6,則可以從\<http://visualstudiogallery.msdn.microsoft.com/18a7db90-6705-4d19-9dd1-0a6c23d0751f>中的 可視化工作室庫獲取檢視生成 T4 範本。</span><span class="sxs-lookup"><span data-stu-id="b9641-283">If you’re using Entity Framework 6 you can get the view generation T4 templates from the Visual Studio Gallery at \<http://visualstudiogallery.msdn.microsoft.com/18a7db90-6705-4d19-9dd1-0a6c23d0751f>.</span></span>

### <a name="24-reducing-the-cost-of-view-generation"></a><span data-ttu-id="b9641-284">2.4 降低檢視產生成本</span><span class="sxs-lookup"><span data-stu-id="b9641-284">2.4 Reducing the cost of view generation</span></span>

<span data-ttu-id="b9641-285">使用預生成的檢視可將檢視生成成本從模型載入(運行時間)移動到設計時間。</span><span class="sxs-lookup"><span data-stu-id="b9641-285">Using pre-generated views moves the cost of view generation from model loading (run time) to design time.</span></span> <span data-ttu-id="b9641-286">雖然這提高了運行時的啟動性能,但在開發時,您仍將經歷視圖生成的痛苦。</span><span class="sxs-lookup"><span data-stu-id="b9641-286">While this improves startup performance at runtime, you will still experience the pain of view generation while you are developing.</span></span> <span data-ttu-id="b9641-287">還有其他幾種技巧可以説明降低在編譯時間和運行時生成檢視的成本。</span><span class="sxs-lookup"><span data-stu-id="b9641-287">There are several additional tricks that can help reduce the cost of view generation, both at compile time and run time.</span></span>

#### <a name="241-using-foreign-key-associations-to-reduce-view-generation-cost"></a><span data-ttu-id="b9641-288">2.4.1 使用外鍵關聯降低檢視生成成本</span><span class="sxs-lookup"><span data-stu-id="b9641-288">2.4.1 Using Foreign Key Associations to reduce view generation cost</span></span>

<span data-ttu-id="b9641-289">我們看到許多案例,將模型中的關聯從獨立協會切換到外國密鑰關聯,從而大大縮短了在視圖生成中花費的時間。</span><span class="sxs-lookup"><span data-stu-id="b9641-289">We have seen a number of cases where switching the associations in the model from Independent Associations to Foreign Key Associations dramatically improved the time spent in view generation.</span></span>

<span data-ttu-id="b9641-290">為了證明這一改進,我們使用 EDMGen 生成了兩個版本的 Navision 模型。</span><span class="sxs-lookup"><span data-stu-id="b9641-290">To demonstrate this improvement, we generated two versions of the Navision model by using EDMGen.</span></span> <span data-ttu-id="b9641-291">*注意:有關 Navision 模型的說明,請參閱附錄 C。*</span><span class="sxs-lookup"><span data-stu-id="b9641-291">*Note: see appendix C for a description of the Navision model.*</span></span> <span data-ttu-id="b9641-292">Navision 模型對本練習非常感興趣,因為它擁有大量實體和它們之間的關係。</span><span class="sxs-lookup"><span data-stu-id="b9641-292">The Navision model is interesting for this exercise due to its very large amount of entities and relationships between them.</span></span>

<span data-ttu-id="b9641-293">此非常大的模型的一個版本是使用外鍵關聯生成的,另一個版本是使用獨立關聯生成的。</span><span class="sxs-lookup"><span data-stu-id="b9641-293">One version of this very large model was generated with Foreign Keys Associations and the other was generated with Independent Associations.</span></span> <span data-ttu-id="b9641-294">然後,我們安排為每個模型生成視圖需要多長時間。</span><span class="sxs-lookup"><span data-stu-id="b9641-294">We then timed how long it took to generate the views for each model.</span></span> <span data-ttu-id="b9641-295">實體框架 5 測試使用類 EntityViewGenerator 的 GenerateViews() 方法產生檢視,而實體框架 6 測試使用類儲存映射專案集合中的 GenerateViews() 方法。</span><span class="sxs-lookup"><span data-stu-id="b9641-295">Entity Framework 5 test used the GenerateViews() method from class EntityViewGenerator to generate the views, while the Entity Framework 6 test used the GenerateViews() method from class StorageMappingItemCollection.</span></span> <span data-ttu-id="b9641-296">這是因為實體框架 6 代碼庫中的代碼重組。</span><span class="sxs-lookup"><span data-stu-id="b9641-296">This due to code restructuring that occurred in the Entity Framework 6 codebase.</span></span>

<span data-ttu-id="b9641-297">使用實體框架 5,使用外鍵生成模型的視圖在實驗室機器中花了 65 分鐘。</span><span class="sxs-lookup"><span data-stu-id="b9641-297">Using Entity Framework 5, view generation for the model with Foreign Keys took 65 minutes in a lab machine.</span></span> <span data-ttu-id="b9641-298">不知道為使用獨立關聯的模型生成檢視需要多長時間。</span><span class="sxs-lookup"><span data-stu-id="b9641-298">It's unknown how long it would have taken to generate the views for the model that used independent associations.</span></span> <span data-ttu-id="b9641-299">在實驗室中重新啟動計算機以安裝每月更新之前,我們讓測試運行了一個多月。</span><span class="sxs-lookup"><span data-stu-id="b9641-299">We left the test running for over a month before the machine was rebooted in our lab to install monthly updates.</span></span>

<span data-ttu-id="b9641-300">使用實體框架 6,使用外鍵生成模型的檢視生成需要 28 秒在同一實驗室機器中。</span><span class="sxs-lookup"><span data-stu-id="b9641-300">Using Entity Framework 6, view generation for the model with Foreign Keys took 28 seconds in the same lab machine.</span></span> <span data-ttu-id="b9641-301">使用獨立關聯的模型的檢視生成需要 58 秒。</span><span class="sxs-lookup"><span data-stu-id="b9641-301">View generation for the model that uses Independent Associations took 58 seconds.</span></span> <span data-ttu-id="b9641-302">實體框架 6 對其檢視生成代碼所做的改進意味著許多專案不需要預生成的檢視來獲取更快的啟動時間。</span><span class="sxs-lookup"><span data-stu-id="b9641-302">The improvements done to Entity Framework 6 on its view generation code mean that many projects won’t need pre-generated views to obtain faster startup times.</span></span>

<span data-ttu-id="b9641-303">請務必指出,實體框架 4 和 5 中的預生成檢視可以使用 EDMGen 或實體框架電動工具完成。</span><span class="sxs-lookup"><span data-stu-id="b9641-303">It’s important to remark that pre-generating views in Entity Framework 4 and 5 can be done with EDMGen or the Entity Framework Power Tools.</span></span> <span data-ttu-id="b9641-304">對於實體框架 6 視圖生成,可以通過實體框架電源工具完成,也可以以程式設計方式完成,如[預生成的映射視圖](~/ef6/fundamentals/performance/pre-generated-views.md)中所述。</span><span class="sxs-lookup"><span data-stu-id="b9641-304">For Entity Framework 6 view generation can be done via the Entity Framework Power Tools or programmatically as described in [Pre-Generated Mapping Views](~/ef6/fundamentals/performance/pre-generated-views.md).</span></span>

##### <a name="2411-how-to-use-foreign-keys-instead-of-independent-associations"></a><span data-ttu-id="b9641-305">2.4.1.1 如何使用外鍵而不是獨立關聯</span><span class="sxs-lookup"><span data-stu-id="b9641-305">2.4.1.1 How to use Foreign Keys instead of Independent Associations</span></span>

<span data-ttu-id="b9641-306">在 Visual Studio 中使用 EDMGen 或實體設計器時,預設情況下,您將獲得 PK,並且只需單個複選框或命令列標誌即可在 SDK 和 I 之間切換。</span><span class="sxs-lookup"><span data-stu-id="b9641-306">When using EDMGen or the Entity Designer in Visual Studio, you get FKs by default, and it only takes a single checkbox or command line flag to switch between FKs and IAs.</span></span>

<span data-ttu-id="b9641-307">如果您有一個大型 Code First 模型,則使用獨立關聯對檢視生成的影響相同。</span><span class="sxs-lookup"><span data-stu-id="b9641-307">If you have a large Code First model, using Independent Associations will have the same effect on view generation.</span></span> <span data-ttu-id="b9641-308">可以通過將外鍵屬性包括在從屬物件的類中來避免這種影響,儘管一些開發人員會認為這會污染其物件模型。</span><span class="sxs-lookup"><span data-stu-id="b9641-308">You can avoid this impact by including Foreign Key properties on the classes for your dependent objects, though some developers will consider this to be polluting their object model.</span></span> <span data-ttu-id="b9641-309">您可以在 中\<http://blog.oneunicorn.com/2011/12/11/whats-the-deal-with-mapping-foreign-keys-using-the-entity-framework/>找到 有關此主題的詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="b9641-309">You can find more information on this subject in \<http://blog.oneunicorn.com/2011/12/11/whats-the-deal-with-mapping-foreign-keys-using-the-entity-framework/>.</span></span>

| <span data-ttu-id="b9641-310">使用時</span><span class="sxs-lookup"><span data-stu-id="b9641-310">When using</span></span>      | <span data-ttu-id="b9641-311">執行方式</span><span class="sxs-lookup"><span data-stu-id="b9641-311">Do this</span></span>                                                                                                                                                                                                                                                                                                                              |
|:----------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="b9641-312">Entity Designer</span><span class="sxs-lookup"><span data-stu-id="b9641-312">Entity Designer</span></span> | <span data-ttu-id="b9641-313">在兩個實體之間添加關聯后,請確保具有引用約束。</span><span class="sxs-lookup"><span data-stu-id="b9641-313">After adding an association between two entities, make sure you have a referential constraint.</span></span> <span data-ttu-id="b9641-314">引用約束告訴實體框架使用外鍵而不是獨立關聯。</span><span class="sxs-lookup"><span data-stu-id="b9641-314">Referential constraints tell Entity Framework to use Foreign Keys instead of Independent Associations.</span></span> <span data-ttu-id="b9641-315">有關其他詳細資訊,\<https://docs.microsoft.com/archive/blogs/efdesign/foreign-keys-in-the-entity-framework>請存取 。</span><span class="sxs-lookup"><span data-stu-id="b9641-315">For additional details visit \<https://docs.microsoft.com/archive/blogs/efdesign/foreign-keys-in-the-entity-framework>.</span></span> |
| <span data-ttu-id="b9641-316">EDMGen</span><span class="sxs-lookup"><span data-stu-id="b9641-316">EDMGen</span></span>          | <span data-ttu-id="b9641-317">當使用 EDMGen 從資料庫生成檔時,您的外鍵將受到尊重並添加到模型中。</span><span class="sxs-lookup"><span data-stu-id="b9641-317">When using EDMGen to generate your files from the database, your Foreign Keys will be respected and added to the model as such.</span></span> <span data-ttu-id="b9641-318">有關 EDMGen 公開的不同選項的更多資訊,[http://msdn.microsoft.com/library/bb387165.aspx](https://msdn.microsoft.com/library/bb387165.aspx)請造訪 。</span><span class="sxs-lookup"><span data-stu-id="b9641-318">For more information on the different options exposed by EDMGen visit [http://msdn.microsoft.com/library/bb387165.aspx](https://msdn.microsoft.com/library/bb387165.aspx).</span></span>                           |
| <span data-ttu-id="b9641-319">Code First</span><span class="sxs-lookup"><span data-stu-id="b9641-319">Code First</span></span>      | <span data-ttu-id="b9641-320">有關在使用代碼優先時如何將外鍵屬性包含在從屬物件上的資訊,請參閱[代碼優先約定](~/ef6/modeling/code-first/conventions/built-in.md)主題的「關係約定」 部分。</span><span class="sxs-lookup"><span data-stu-id="b9641-320">See the "Relationship Convention" section of the [Code First Conventions](~/ef6/modeling/code-first/conventions/built-in.md) topic for information on how to include foreign key properties on dependent objects when using Code First.</span></span>                                                                                              |

#### <a name="242-moving-your-model-to-a-separate-assembly"></a><span data-ttu-id="b9641-321">2.4.2 將模型移動到單獨的裝配體</span><span class="sxs-lookup"><span data-stu-id="b9641-321">2.4.2 Moving your model to a separate assembly</span></span>

<span data-ttu-id="b9641-322">當模型直接包含在應用程式的專案中,並且通過預生成事件或 T4 範本生成檢視時,每當重建專案時,即使模型未更改,也會進行檢視生成和驗證。</span><span class="sxs-lookup"><span data-stu-id="b9641-322">When your model is included directly in your application's project and you generate views through a pre-build event or a T4 template, view generation and validation will take place whenever the project is rebuilt, even if the model wasn't changed.</span></span> <span data-ttu-id="b9641-323">如果將模型移動到單獨的程式集並從應用程式的專案中引用它,則可以對應用程式進行其他更改,而無需重新生成包含模型的專案。</span><span class="sxs-lookup"><span data-stu-id="b9641-323">If you move the model to a separate assembly and reference it from your application's project, you can make other changes to your application without needing to rebuild the project containing the model.</span></span>

<span data-ttu-id="b9641-324">*注意:* 將模型移動到單獨的程式集時,請記住將模型的連接字串複製到用戶端專案的應用程式配置檔中。</span><span class="sxs-lookup"><span data-stu-id="b9641-324">*Note:*  when moving your model to separate assemblies remember to copy the connection strings for the model into the application configuration file of the client project.</span></span>

#### <a name="243-disable-validation-of-an-edmx-based-model"></a><span data-ttu-id="b9641-325">2.4.3 停用基於 edmx 的模型驗證</span><span class="sxs-lookup"><span data-stu-id="b9641-325">2.4.3 Disable validation of an edmx-based model</span></span>

<span data-ttu-id="b9641-326">EDMX 模型在編譯時進行驗證,即使模型保持不變。</span><span class="sxs-lookup"><span data-stu-id="b9641-326">EDMX models are validated at compile time, even if the model is unchanged.</span></span> <span data-ttu-id="b9641-327">如果模型已過驗證,則可以通過在屬性視窗中將"在生成上驗證"屬性設置為 false 來禁止編譯時驗證。</span><span class="sxs-lookup"><span data-stu-id="b9641-327">If your model has already been validated, you can suppress validation at compile time by setting the "Validate on Build" property to false in the properties window.</span></span> <span data-ttu-id="b9641-328">更改映射或模型時,可以臨時重新啟用驗證以驗證更改。</span><span class="sxs-lookup"><span data-stu-id="b9641-328">When you change your mapping or model, you can temporarily re-enable validation to verify your changes.</span></span>

<span data-ttu-id="b9641-329">請注意,實體框架 6 的實體框架設計器的性能得到了提高," 在生成時驗證" 的成本比設計器的早期版本要低得多。</span><span class="sxs-lookup"><span data-stu-id="b9641-329">Note that performance improvements were made to the Entity Framework Designer for Entity Framework 6, and the cost of the “Validate on Build” is much lower than in previous versions of the designer.</span></span>

## <a name="3-caching-in-the-entity-framework"></a><span data-ttu-id="b9641-330">3 實體框架中快取</span><span class="sxs-lookup"><span data-stu-id="b9641-330">3 Caching in the Entity Framework</span></span>

<span data-ttu-id="b9641-331">實體框架具有以下形式的快取記憶體:</span><span class="sxs-lookup"><span data-stu-id="b9641-331">Entity Framework has the following forms of caching built-in:</span></span>

1.  <span data-ttu-id="b9641-332">物件快取 - 內置到 ObjectContext 實體中的 Object StateManager 在使用該實例檢索的物件的記憶體中追蹤該物件。</span><span class="sxs-lookup"><span data-stu-id="b9641-332">Object caching – the ObjectStateManager built into an ObjectContext instance keeps track in memory of the objects that have been retrieved using that instance.</span></span> <span data-ttu-id="b9641-333">這也稱為第一級緩存。</span><span class="sxs-lookup"><span data-stu-id="b9641-333">This is also known as first-level cache.</span></span>
2.  <span data-ttu-id="b9641-334">查詢計劃快取 - 多次執行查詢時重用生成的儲存命令。</span><span class="sxs-lookup"><span data-stu-id="b9641-334">Query Plan Caching - reusing the generated store command when a query is executed more than once.</span></span>
3.  <span data-ttu-id="b9641-335">中繼資料快取 - 跨與同一模型的不同連接共用模型的元資料。</span><span class="sxs-lookup"><span data-stu-id="b9641-335">Metadata caching - sharing the metadata for a model across different connections to the same model.</span></span>

<span data-ttu-id="b9641-336">除了 EF 開箱即用的緩存外,一種稱為包裝提供程式的特殊 ADO.NET 數據提供者還可用於擴展實體框架,並緩存從資料庫檢索的結果(也稱為二級緩存)。</span><span class="sxs-lookup"><span data-stu-id="b9641-336">Besides the caches that EF provides out of the box, a special kind of ADO.NET data provider known as a wrapping provider can also be used to extend Entity Framework with a cache for the results retrieved from the database, also known as second-level caching.</span></span>

### <a name="31-object-caching"></a><span data-ttu-id="b9641-337">3.1 物件快取</span><span class="sxs-lookup"><span data-stu-id="b9641-337">3.1 Object Caching</span></span>

<span data-ttu-id="b9641-338">預設情況下,當在查詢結果中返回實體時,就在 EF 實現該實體之前,ObjectContext 將檢查具有相同金鑰的實體是否已載入到其 Object StateManager 中。</span><span class="sxs-lookup"><span data-stu-id="b9641-338">By default when an entity is returned in the results of a query, just before EF materializes it, the ObjectContext will check if an entity with the same key has already been loaded into its ObjectStateManager.</span></span> <span data-ttu-id="b9641-339">如果具有相同金鑰的實體已存在,EF 將在查詢結果中包括該密鑰。</span><span class="sxs-lookup"><span data-stu-id="b9641-339">If an entity with the same keys is already present EF will include it in the results of the query.</span></span> <span data-ttu-id="b9641-340">儘管 EF 仍將針對資料庫發出查詢,但此行為可以繞過實體多次實現的大部分成本。</span><span class="sxs-lookup"><span data-stu-id="b9641-340">Although EF will still issue the query against the database, this behavior can bypass much of the cost of materializing the entity multiple times.</span></span>

#### <a name="311-getting-entities-from-the-object-cache-using-dbcontext-find"></a><span data-ttu-id="b9641-341">3.1.1 使用 DbContext 尋找從物件快取實體</span><span class="sxs-lookup"><span data-stu-id="b9641-341">3.1.1 Getting entities from the object cache using DbContext Find</span></span>

<span data-ttu-id="b9641-342">與常規查詢不同,DbSet 中的 Find 方法(在 EF 4.1 中首次包含 API)將在記憶體中執行搜索,甚至針對資料庫發出查詢。</span><span class="sxs-lookup"><span data-stu-id="b9641-342">Unlike a regular query, the Find method in DbSet (APIs included for the first time in EF 4.1) will perform a search in memory before even issuing the query against the database.</span></span> <span data-ttu-id="b9641-343">請務必注意,兩個不同的 ObjectObjectContext 實例將具有兩個不同的 Object StateManager 實例,這意味著它們具有單獨的物件緩存。</span><span class="sxs-lookup"><span data-stu-id="b9641-343">It’s important to note that two different ObjectContext instances will have two different ObjectStateManager instances, meaning that they have separate object caches.</span></span>

<span data-ttu-id="b9641-344">Find 使用主鍵值嘗試查找由上下文跟蹤的實體。</span><span class="sxs-lookup"><span data-stu-id="b9641-344">Find uses the primary key value to attempt to find an entity tracked by the context.</span></span> <span data-ttu-id="b9641-345">如果實體不在上下文中,則將針對資料庫執行和評估查詢,如果在上下文中或資料庫中找不到該實體,則返回 null。</span><span class="sxs-lookup"><span data-stu-id="b9641-345">If the entity is not in the context then a query will be executed and evaluated against the database, and null is returned if the entity is not found in the context or in the database.</span></span> <span data-ttu-id="b9641-346">請注意,Find 還會返回已添加到上下文但尚未保存到資料庫的實體。</span><span class="sxs-lookup"><span data-stu-id="b9641-346">Note that Find also returns entities that have been added to the context but have not yet been saved to the database.</span></span>

<span data-ttu-id="b9641-347">使用 Find 時需要考慮性能。</span><span class="sxs-lookup"><span data-stu-id="b9641-347">There is a performance consideration to be taken when using Find.</span></span> <span data-ttu-id="b9641-348">默認情況下,調用此方法將觸發物件緩存的驗證,以檢測仍掛起的提交到資料庫的更改。</span><span class="sxs-lookup"><span data-stu-id="b9641-348">Invocations to this method by default will trigger a validation of the object cache in order to detect changes that are still pending commit to the database.</span></span> <span data-ttu-id="b9641-349">如果物件緩存或大型物件圖形中有大量物件添加到物件緩存中,此過程可能非常昂貴,但也可以禁用。</span><span class="sxs-lookup"><span data-stu-id="b9641-349">This process can be very expensive if there are a very large number of objects in the object cache or in a large object graph being added to the object cache, but it can also be disabled.</span></span> <span data-ttu-id="b9641-350">在某些情況下,在禁用自動檢測更改時,在調用 Find 方法時,您可能會感覺到在調用 Find 方法時,差異程度超過一個數量級。</span><span class="sxs-lookup"><span data-stu-id="b9641-350">In certain cases, you may perceive over an order of magnitude of difference in calling the Find method when you disable auto detect changes.</span></span> <span data-ttu-id="b9641-351">但是,當對象實際位於緩存中時,以及必須從資料庫中檢索物件時,會感知到第二個數量級。</span><span class="sxs-lookup"><span data-stu-id="b9641-351">Yet a second order of magnitude is perceived when the object actually is in the cache versus when the object has to be retrieved from the database.</span></span> <span data-ttu-id="b9641-352">下面是一個範例圖,使用我們的一些微基準(以毫秒表示)進行測量,負載為 5000 個實體:</span><span class="sxs-lookup"><span data-stu-id="b9641-352">Here is an example graph with measurements taken using some of our microbenchmarks, expressed in milliseconds, with a load of 5000 entities:</span></span>

<span data-ttu-id="b9641-353">![.NET 4.5 對數刻度](~/ef6/media/net45logscale.png ".NET 4.5 - 對數刻度")</span><span class="sxs-lookup"><span data-stu-id="b9641-353">![.NET 4.5 logarithmic scale](~/ef6/media/net45logscale.png ".NET 4.5 - logarithmic scale")</span></span>

<span data-ttu-id="b9641-354">關閉自動偵測變更的尋找範例:</span><span class="sxs-lookup"><span data-stu-id="b9641-354">Example of Find with auto-detect changes disabled:</span></span>

``` csharp
    context.Configuration.AutoDetectChangesEnabled = false;
    var product = context.Products.Find(productId);
    context.Configuration.AutoDetectChangesEnabled = true;
    ...
```

<span data-ttu-id="b9641-355">使用 Find 方法時必須考慮的是:</span><span class="sxs-lookup"><span data-stu-id="b9641-355">What you have to consider when using the Find method is:</span></span>

1.  <span data-ttu-id="b9641-356">如果物件不在緩存中,則 Find 的好處將被否定,但語法仍比按鍵查詢更簡單。</span><span class="sxs-lookup"><span data-stu-id="b9641-356">If the object is not in the cache the benefits of Find are negated, but the syntax is still simpler than a query by key.</span></span>
2.  <span data-ttu-id="b9641-357">如果啟用自動檢測更改,Find 方法的成本可能會增加一個數量級,甚至更多取決於模型的複雜性和物件緩存中的實體數量。</span><span class="sxs-lookup"><span data-stu-id="b9641-357">If auto detect changes is enabled the cost of the Find method may increase by one order of magnitude, or even more depending on the complexity of your model and the amount of entities in your object cache.</span></span>

<span data-ttu-id="b9641-358">此外,請記住,Find 僅返回要查找的實體,如果關聯實體尚未在物件緩存中,則不會自動載入它們。</span><span class="sxs-lookup"><span data-stu-id="b9641-358">Also, keep in mind that Find only returns the entity you are looking for and it does not automatically loads its associated entities if they are not already in the object cache.</span></span> <span data-ttu-id="b9641-359">如果需要檢索關聯的實體,可以使用按鍵進行查詢,並進行熱切載入。</span><span class="sxs-lookup"><span data-stu-id="b9641-359">If you need to retrieve associated entities, you can use a query by key with eager loading.</span></span> <span data-ttu-id="b9641-360">有關詳細資訊,請參閱**8.1 延遲載入與熱載入**。</span><span class="sxs-lookup"><span data-stu-id="b9641-360">For more information see **8.1 Lazy Loading vs. Eager Loading**.</span></span>

#### <a name="312-performance-issues-when-the-object-cache-has-many-entities"></a><span data-ttu-id="b9641-361">3.1.2 當物件緩存具有多個實體時的性能問題</span><span class="sxs-lookup"><span data-stu-id="b9641-361">3.1.2 Performance issues when the object cache has many entities</span></span>

<span data-ttu-id="b9641-362">物件緩存有助於提高實體框架的總體回應能力。</span><span class="sxs-lookup"><span data-stu-id="b9641-362">The object cache helps to increase the overall responsiveness of Entity Framework.</span></span> <span data-ttu-id="b9641-363">但是,當物件緩存載入的實體量非常大時,可能會影響某些操作,如添加、刪除、查找、輸入、保存更改等。</span><span class="sxs-lookup"><span data-stu-id="b9641-363">However, when the object cache has a very large amount of entities loaded it may affect certain operations such as Add, Remove, Find, Entry, SaveChanges and more.</span></span> <span data-ttu-id="b9641-364">特別是,觸發調用檢測更改的操作將受到非常大的物件緩存的負面影響。</span><span class="sxs-lookup"><span data-stu-id="b9641-364">In particular, operations that trigger a call to DetectChanges will be negatively affected by very large object caches.</span></span> <span data-ttu-id="b9641-365">檢測更改將物件圖形與物件狀態管理器同步,其性能將直接由物件圖形的大小決定。</span><span class="sxs-lookup"><span data-stu-id="b9641-365">DetectChanges synchronizes the object graph with the object state manager and its performance will determined directly by the size of the object graph.</span></span> <span data-ttu-id="b9641-366">有關偵測變更的詳細資訊,請參考[追蹤 POCO 實體中的變更](https://msdn.microsoft.com/library/dd456848.aspx)。</span><span class="sxs-lookup"><span data-stu-id="b9641-366">For more information about DetectChanges, see [Tracking Changes in POCO Entities](https://msdn.microsoft.com/library/dd456848.aspx).</span></span>

<span data-ttu-id="b9641-367">使用實體框架 6 時,開發人員可以直接在 DbSet 上調用 AddRange 和 RemoveRange,而不是在集合上反覆運算,並按實例調用一次 Add。</span><span class="sxs-lookup"><span data-stu-id="b9641-367">When using Entity Framework 6, developers are able to call AddRange and RemoveRange directly on a DbSet, instead of iterating on a collection and calling Add once per instance.</span></span> <span data-ttu-id="b9641-368">使用範圍方法的優點是,檢測更改的成本僅針對整個實體集支付一次,而不是每個添加的實體支付一次。</span><span class="sxs-lookup"><span data-stu-id="b9641-368">The advantage of using the range methods is that the cost of DetectChanges is only paid once for the entire set of entities as opposed to once per each added entity.</span></span>

### <a name="32-query-plan-caching"></a><span data-ttu-id="b9641-369">3.2 查詢排程快取</span><span class="sxs-lookup"><span data-stu-id="b9641-369">3.2 Query Plan Caching</span></span>

<span data-ttu-id="b9641-370">第一次執行查詢時,它會通過內部計劃編譯器將概念查詢轉換為儲存命令(例如,在對 SQL Server 運行時執行的 T-SQL)。</span><span class="sxs-lookup"><span data-stu-id="b9641-370">The first time a query is executed, it goes through the internal plan compiler to translate the conceptual query into the store command (for example, the T-SQL which is executed when run against SQL Server).</span></span><span data-ttu-id="b9641-371">如果啟用了查詢計劃緩存,則下次執行查詢時,將直接從查詢計劃緩存中檢索存儲命令以執行,繞過計劃編譯器。</span><span class="sxs-lookup"><span data-stu-id="b9641-371">  If query plan caching is enabled, the next time the query is executed the store command is retrieved directly from the query plan cache for execution, bypassing the plan compiler.</span></span>

<span data-ttu-id="b9641-372">查詢計劃緩存在同一 AppDomain 中的 ObjectContext 實例之間共用。</span><span class="sxs-lookup"><span data-stu-id="b9641-372">The query plan cache is shared across ObjectContext instances within the same AppDomain.</span></span> <span data-ttu-id="b9641-373">您無需保留 ObjectContext 實例即可從查詢計劃快取中獲益。</span><span class="sxs-lookup"><span data-stu-id="b9641-373">You don't need to hold onto an ObjectContext instance to benefit from query plan caching.</span></span>

#### <a name="321-some-notes-about-query-plan-caching"></a><span data-ttu-id="b9641-374">3.2.1 關於查詢計劃緩存的一些註釋</span><span class="sxs-lookup"><span data-stu-id="b9641-374">3.2.1 Some notes about Query Plan Caching</span></span>

-   <span data-ttu-id="b9641-375">查詢計劃緩存為所有查詢類型共用:實體 SQL、LINQ 到實體和編譯查詢物件。</span><span class="sxs-lookup"><span data-stu-id="b9641-375">The query plan cache is shared for all query types: Entity SQL, LINQ to Entities, and CompiledQuery objects.</span></span>
-   <span data-ttu-id="b9641-376">默認情況下,為實體 SQL 查詢啟用查詢計劃快取,無論是透過實體命令還是透過物件查詢執行。</span><span class="sxs-lookup"><span data-stu-id="b9641-376">By default, query plan caching is enabled for Entity SQL queries, whether executed through an EntityCommand or through an ObjectQuery.</span></span> <span data-ttu-id="b9641-377">默認情況下,在 .NET 4.5 上的實體框架中,在實體框架中,在實體框架中,LINQ 對實體查詢也啟用了它,</span><span class="sxs-lookup"><span data-stu-id="b9641-377">It is also enabled by default for LINQ to Entities queries in Entity Framework on .NET 4.5, and in Entity Framework 6</span></span>
    -   <span data-ttu-id="b9641-378">通過將啟用計劃緩存屬性(在實體命令或物件查詢上)設置為 false,可以禁用查詢計劃緩存。</span><span class="sxs-lookup"><span data-stu-id="b9641-378">Query plan caching can be disabled by setting the EnablePlanCaching property (on EntityCommand or ObjectQuery) to false.</span></span> <span data-ttu-id="b9641-379">例如：</span><span class="sxs-lookup"><span data-stu-id="b9641-379">For example:</span></span>
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
-   <span data-ttu-id="b9641-380">對於參數化查詢,更改參數的值仍將命中緩存的查詢。</span><span class="sxs-lookup"><span data-stu-id="b9641-380">For parameterized queries, changing the parameter's value will still hit the cached query.</span></span> <span data-ttu-id="b9641-381">但是,更改參數的分面(例如,大小、精度或比例)將擊中緩存中的不同條目。</span><span class="sxs-lookup"><span data-stu-id="b9641-381">But changing a parameter's facets (for example, size, precision, or scale) will hit a different entry in the cache.</span></span>
-   <span data-ttu-id="b9641-382">使用實體 SQL 時,查詢字串是密鑰的一部分。</span><span class="sxs-lookup"><span data-stu-id="b9641-382">When using Entity SQL, the query string is part of the key.</span></span> <span data-ttu-id="b9641-383">更改查詢將會導致不同的緩存條目,即使查詢在功能上等效。</span><span class="sxs-lookup"><span data-stu-id="b9641-383">Changing the query at all will result in different cache entries, even if the queries are functionally equivalent.</span></span> <span data-ttu-id="b9641-384">這包括對套管或空白的更改。</span><span class="sxs-lookup"><span data-stu-id="b9641-384">This includes changes to casing or whitespace.</span></span>
-   <span data-ttu-id="b9641-385">使用 LINQ 時,將處理查詢以生成密鑰的一部分。</span><span class="sxs-lookup"><span data-stu-id="b9641-385">When using LINQ, the query is processed to generate a part of the key.</span></span> <span data-ttu-id="b9641-386">因此,更改 LINQ 表達式將生成不同的鍵。</span><span class="sxs-lookup"><span data-stu-id="b9641-386">Changing the LINQ expression will therefore generate a different key.</span></span>
-   <span data-ttu-id="b9641-387">可能適用其他技術限制;有關詳細資訊,請參閱自動編譯查詢。</span><span class="sxs-lookup"><span data-stu-id="b9641-387">Other technical limitations may apply; see Autocompiled Queries for more details.</span></span>

#### <a name="322-cache-eviction-algorithm"></a><span data-ttu-id="b9641-388">3.2.2 緩存逐出演演算法</span><span class="sxs-lookup"><span data-stu-id="b9641-388">3.2.2      Cache eviction algorithm</span></span>

<span data-ttu-id="b9641-389">瞭解內部演演演算法的工作原理將説明您確定何時啟用或禁用查詢計劃緩存。</span><span class="sxs-lookup"><span data-stu-id="b9641-389">Understanding how the internal algorithm works will help you figure out when to enable or disable query plan caching.</span></span> <span data-ttu-id="b9641-390">清理演演算法如下:</span><span class="sxs-lookup"><span data-stu-id="b9641-390">The cleanup algorithm is as follows:</span></span>

1.  <span data-ttu-id="b9641-391">一旦緩存包含一組條目數 (800),我們將啟動一個計時器,定期(每分鐘一次)掃描緩存。</span><span class="sxs-lookup"><span data-stu-id="b9641-391">Once the cache contains a set number of entries (800), we start a timer that periodically (once-per-minute) sweeps the cache.</span></span>
2.  <span data-ttu-id="b9641-392">在快取掃描期間,條目會以 LFRU(最不頻繁和最近使用)為基礎從緩存中刪除。</span><span class="sxs-lookup"><span data-stu-id="b9641-392">During cache sweeps, entries are removed from the cache on a LFRU (Least frequently – recently used) basis.</span></span> <span data-ttu-id="b9641-393">此演演演算法在決定彈出哪些條目時,會考慮命中計數和年齡。</span><span class="sxs-lookup"><span data-stu-id="b9641-393">This algorithm takes both hit count and age into account when deciding which entries are ejected.</span></span>
3.  <span data-ttu-id="b9641-394">在每個緩存掃描結束時,緩存再次包含 800 個條目。</span><span class="sxs-lookup"><span data-stu-id="b9641-394">At the end of each cache sweep, the cache again contains 800 entries.</span></span>

<span data-ttu-id="b9641-395">在確定要驅逐的條目時,所有緩存條目都一視同仁。</span><span class="sxs-lookup"><span data-stu-id="b9641-395">All cache entries are treated equally when determining which entries to evict.</span></span> <span data-ttu-id="b9641-396">這意味著編譯查詢的儲存命令與實體 SQL 查詢的儲存命令具有相同的逐出機會。</span><span class="sxs-lookup"><span data-stu-id="b9641-396">This means the store command for a CompiledQuery has the same chance of eviction as the store command for an Entity SQL query.</span></span>

<span data-ttu-id="b9641-397">請注意,緩存驅逐計時器在緩存中有 800 個實體時被踢中,但緩存僅在啟動此計時器 60 秒後被掃描。</span><span class="sxs-lookup"><span data-stu-id="b9641-397">Note that the cache eviction timer is kicked in when there are 800 entities in the cache, but the cache is only swept 60 seconds after this timer is started.</span></span> <span data-ttu-id="b9641-398">這意味著,最多 60 秒,您的緩存可能會增長到相當大。</span><span class="sxs-lookup"><span data-stu-id="b9641-398">That means that for up to 60 seconds your cache may grow to be quite large.</span></span>

#### <a name="323-test-metrics-demonstrating-query-plan-caching-performance"></a><span data-ttu-id="b9641-399">3.2.3 展示查詢計劃快取效能的測試指標</span><span class="sxs-lookup"><span data-stu-id="b9641-399">3.2.3       Test Metrics demonstrating query plan caching performance</span></span>

<span data-ttu-id="b9641-400">為了演示查詢計劃緩存對應用程式性能的影響,我們執行了一個測試,其中針對 Navision 模型執行了許多實體 SQL 查詢。</span><span class="sxs-lookup"><span data-stu-id="b9641-400">To demonstrate the effect of query plan caching on your application's performance, we performed a test where we executed a number of Entity SQL queries against the Navision model.</span></span> <span data-ttu-id="b9641-401">有關 Navision 模型的說明以及已執行的查詢類型,請參閱附錄。</span><span class="sxs-lookup"><span data-stu-id="b9641-401">See the appendix for a description of the Navision model and the types of queries which were executed.</span></span> <span data-ttu-id="b9641-402">在此測試中,我們首先反覆運算查詢清單,並執行每個查詢一次以將其添加到緩存(如果啟用了緩存)。</span><span class="sxs-lookup"><span data-stu-id="b9641-402">In this test, we first iterate through the list of queries and execute each one once to add them to the cache (if caching is enabled).</span></span> <span data-ttu-id="b9641-403">此步驟是非時無刻的。</span><span class="sxs-lookup"><span data-stu-id="b9641-403">This step is untimed.</span></span> <span data-ttu-id="b9641-404">接下來,我們將主線程休眠超過 60 秒,以便進行緩存掃描;最後,我們第二次遍通清單以執行緩存的查詢。</span><span class="sxs-lookup"><span data-stu-id="b9641-404">Next, we sleep the main thread for over 60 seconds to allow cache sweeping to take place; finally, we iterate through the list a 2nd time to execute the cached queries.</span></span> <span data-ttu-id="b9641-405">此外,在執行每組查詢之前刷新 SQL Server 計劃緩存,以便我們獲取的時間準確反映查詢計劃緩存提供的好處。</span><span class="sxs-lookup"><span data-stu-id="b9641-405">Additionally, the SQL Server plan cache is flushed before each set of queries is executed so that the times we obtain accurately reflect the benefit given by the query plan cache.</span></span>

##### <a name="3231-test-results"></a><span data-ttu-id="b9641-406">3.2.3.1 測試結果</span><span class="sxs-lookup"><span data-stu-id="b9641-406">3.2.3.1       Test Results</span></span>

| <span data-ttu-id="b9641-407">測試</span><span class="sxs-lookup"><span data-stu-id="b9641-407">Test</span></span>                                                                   | <span data-ttu-id="b9641-408">EF5 無快取</span><span class="sxs-lookup"><span data-stu-id="b9641-408">EF5 no cache</span></span> | <span data-ttu-id="b9641-409">EF5 快取</span><span class="sxs-lookup"><span data-stu-id="b9641-409">EF5 cached</span></span> | <span data-ttu-id="b9641-410">EF6 無快取</span><span class="sxs-lookup"><span data-stu-id="b9641-410">EF6 no cache</span></span> | <span data-ttu-id="b9641-411">EF6 快取</span><span class="sxs-lookup"><span data-stu-id="b9641-411">EF6 cached</span></span> |
|:-----------------------------------------------------------------------|:-------------|:-----------|:-------------|:-----------|
| <span data-ttu-id="b9641-412">列舉所有 18723 個查詢</span><span class="sxs-lookup"><span data-stu-id="b9641-412">Enumerating all 18723 queries</span></span>                                          | <span data-ttu-id="b9641-413">124</span><span class="sxs-lookup"><span data-stu-id="b9641-413">124</span></span>          | <span data-ttu-id="b9641-414">125.4</span><span class="sxs-lookup"><span data-stu-id="b9641-414">125.4</span></span>      | <span data-ttu-id="b9641-415">124.3</span><span class="sxs-lookup"><span data-stu-id="b9641-415">124.3</span></span>        | <span data-ttu-id="b9641-416">125.3</span><span class="sxs-lookup"><span data-stu-id="b9641-416">125.3</span></span>      |
| <span data-ttu-id="b9641-417">避免掃描(僅前 800 個查詢,無論複雜性如何)</span><span class="sxs-lookup"><span data-stu-id="b9641-417">Avoiding sweep (just the first 800 queries, regardless of complexity)</span></span>  | <span data-ttu-id="b9641-418">41.7</span><span class="sxs-lookup"><span data-stu-id="b9641-418">41.7</span></span>         | <span data-ttu-id="b9641-419">5.5</span><span class="sxs-lookup"><span data-stu-id="b9641-419">5.5</span></span>        | <span data-ttu-id="b9641-420">40.5</span><span class="sxs-lookup"><span data-stu-id="b9641-420">40.5</span></span>         | <span data-ttu-id="b9641-421">5.4</span><span class="sxs-lookup"><span data-stu-id="b9641-421">5.4</span></span>        |
| <span data-ttu-id="b9641-422">只聚合子的檢查(總計 178 個 - 避免掃描 )</span><span class="sxs-lookup"><span data-stu-id="b9641-422">Just the AggregatingSubtotals queries (178 total - which avoids sweep)</span></span> | <span data-ttu-id="b9641-423">39.5</span><span class="sxs-lookup"><span data-stu-id="b9641-423">39.5</span></span>         | <span data-ttu-id="b9641-424">4.5</span><span class="sxs-lookup"><span data-stu-id="b9641-424">4.5</span></span>        | <span data-ttu-id="b9641-425">38.1</span><span class="sxs-lookup"><span data-stu-id="b9641-425">38.1</span></span>         | <span data-ttu-id="b9641-426">4.6</span><span class="sxs-lookup"><span data-stu-id="b9641-426">4.6</span></span>        |

<span data-ttu-id="b9641-427">*所有時間(以秒為單位)。*</span><span class="sxs-lookup"><span data-stu-id="b9641-427">*All times in seconds.*</span></span>

<span data-ttu-id="b9641-428">道德 - 在執行大量不同的查詢(例如,動態創建的查詢)時,緩存不起作用,並且生成的緩存刷新可以使從計劃緩存中從實際使用中受益最大的查詢保持。</span><span class="sxs-lookup"><span data-stu-id="b9641-428">Moral - when executing lots of distinct queries (for example,  dynamically created queries), caching doesn't help and the resulting flushing of the cache can keep the queries that would benefit the most from plan caching from actually using it.</span></span>

<span data-ttu-id="b9641-429">聚合子總計查詢是測試的查詢中最複雜的。</span><span class="sxs-lookup"><span data-stu-id="b9641-429">The AggregatingSubtotals queries are the most complex of the queries we tested with.</span></span> <span data-ttu-id="b9641-430">正如預期的那樣,查詢越複雜,您從查詢計劃緩存中看到的好處就越大。</span><span class="sxs-lookup"><span data-stu-id="b9641-430">As expected, the more complex the query is, the more benefit you will see from query plan caching.</span></span>

<span data-ttu-id="b9641-431">由於編譯查詢實際上是一個 LINQ 查詢,其計劃緩存,因此編譯查詢與等效實體 SQL 查詢的比較應具有類似的結果。</span><span class="sxs-lookup"><span data-stu-id="b9641-431">Because a CompiledQuery is really a LINQ query with its plan cached, the comparison of a CompiledQuery versus the equivalent Entity SQL query should have similar results.</span></span> <span data-ttu-id="b9641-432">事實上,如果應用具有大量動態實體 SQL 查詢,則使用查詢填充緩存也會有效地導致編譯查詢在從緩存中刷新時"去編譯」。。</span><span class="sxs-lookup"><span data-stu-id="b9641-432">In fact, if an app has lots of dynamic Entity SQL queries, filling the cache with queries will also effectively cause CompiledQueries to “decompile” when they are flushed from the cache.</span></span> <span data-ttu-id="b9641-433">在這種情況下,可以通過禁用動態查詢的緩存來確定編譯查詢的優先順序,從而提高性能。</span><span class="sxs-lookup"><span data-stu-id="b9641-433">In this scenario, performance may be improved by disabling caching on the dynamic queries to prioritize the CompiledQueries.</span></span> <span data-ttu-id="b9641-434">當然,更好的是重寫應用以使用參數化查詢而不是動態查詢。</span><span class="sxs-lookup"><span data-stu-id="b9641-434">Better yet, of course, would be to rewrite the app to use parameterized queries instead of dynamic queries.</span></span>

### <a name="33-using-compiledquery-to-improve-performance-with-linq-queries"></a><span data-ttu-id="b9641-435">3.3 使用編譯查詢提高 LINQ 查詢的效能</span><span class="sxs-lookup"><span data-stu-id="b9641-435">3.3 Using CompiledQuery to improve performance with LINQ queries</span></span>

<span data-ttu-id="b9641-436">我們的測試表明,使用編譯查詢可以帶來 7% 的好處比自動編譯的 LINQ 查詢;這意味著您將在實體框架堆疊中執行代碼的時間減少7%;這並不意味著您的應用程式將加快 7%。</span><span class="sxs-lookup"><span data-stu-id="b9641-436">Our tests indicate that using CompiledQuery can bring a benefit of 7% over autocompiled LINQ queries; this means that you’ll spend 7% less time executing code from the Entity Framework stack; it does not mean your application will be 7% faster.</span></span> <span data-ttu-id="b9641-437">一般來說,在 EF 5.0 中寫入和維護編譯查詢物件的成本與收益相比可能不值得麻煩。</span><span class="sxs-lookup"><span data-stu-id="b9641-437">Generally speaking, the cost of writing and maintaining CompiledQuery objects in EF 5.0 may not be worth the trouble when compared to the benefits.</span></span> <span data-ttu-id="b9641-438">您的里程可能會有所不同,因此,如果您的專案需要額外的推送,請執行此選項。</span><span class="sxs-lookup"><span data-stu-id="b9641-438">Your mileage may vary, so exercise this option if your project requires the extra push.</span></span> <span data-ttu-id="b9641-439">請注意,編譯查詢僅與物件上下文派生模型相容,並且與 DbContext 派生的模型不相容。</span><span class="sxs-lookup"><span data-stu-id="b9641-439">Note that CompiledQueries are only compatible with ObjectContext-derived models, and not compatible with DbContext-derived models.</span></span>

<span data-ttu-id="b9641-440">有關創建和調用編譯查詢的詳細資訊,請參閱[編譯查詢(LINQ 到實體)。](https://msdn.microsoft.com/library/bb896297.aspx)</span><span class="sxs-lookup"><span data-stu-id="b9641-440">For more information on creating and invoking a CompiledQuery, see [Compiled Queries (LINQ to Entities)](https://msdn.microsoft.com/library/bb896297.aspx).</span></span>

<span data-ttu-id="b9641-441">使用 Compilequery 時,您需要考慮兩個注意事項,即使用靜態實例的要求及其可組合性問題。</span><span class="sxs-lookup"><span data-stu-id="b9641-441">There are two considerations you have to take when using a CompiledQuery, namely the requirement to use static instances and the problems they have with composability.</span></span> <span data-ttu-id="b9641-442">下面對這兩個注意事項進行了深入解釋。</span><span class="sxs-lookup"><span data-stu-id="b9641-442">Here follows an in-depth explanation of these two considerations.</span></span>

#### <a name="331-use-static-compiledquery-instances"></a><span data-ttu-id="b9641-443">3.3.1 使用靜態編譯查詢實例</span><span class="sxs-lookup"><span data-stu-id="b9641-443">3.3.1       Use static CompiledQuery instances</span></span>

<span data-ttu-id="b9641-444">由於編譯 LINQ 查詢是一個耗時的過程,因此我們不希望每次需要從資料庫獲取數據時都這樣做。</span><span class="sxs-lookup"><span data-stu-id="b9641-444">Since compiling a LINQ query is a time-consuming process, we don’t want to do it every time we need to fetch data from the database.</span></span> <span data-ttu-id="b9641-445">編譯查詢實例允許您編譯一次並運行多次,但您必須小心並採購,以便每次都重複使用同一個編譯查詢實例,而不是反覆編譯它。</span><span class="sxs-lookup"><span data-stu-id="b9641-445">CompiledQuery instances allow you to compile once and run multiple times, but you have to be careful and procure to re-use the same CompiledQuery instance every time instead of compiling it over and over again.</span></span> <span data-ttu-id="b9641-446">使用靜態成員來存儲編譯查詢實例是必要的;否則,您不會看到任何好處。</span><span class="sxs-lookup"><span data-stu-id="b9641-446">The use of static members to store the CompiledQuery instances becomes necessary; otherwise you won’t see any benefit.</span></span>

<span data-ttu-id="b9641-447">例如,假設您的頁面具有以下方法正文來處理顯示所選類別的產品:</span><span class="sxs-lookup"><span data-stu-id="b9641-447">For example, suppose your page has the following method body to handle displaying the products for the selected category:</span></span>

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

<span data-ttu-id="b9641-448">在這種情況下,每次調用方法時,您都會動態創建新的編譯查詢實例。</span><span class="sxs-lookup"><span data-stu-id="b9641-448">In this case, you will create a new CompiledQuery instance on the fly every time the method is called.</span></span> <span data-ttu-id="b9641-449">CompiledQuery 不會通過從查詢計劃緩存中檢索儲存命令來看到性能優勢,而是每次創建新實例時都會通過計劃編譯器。</span><span class="sxs-lookup"><span data-stu-id="b9641-449">Instead of seeing performance benefits by retrieving the store command from the query plan cache, the CompiledQuery will go through the plan compiler every time a new instance is created.</span></span> <span data-ttu-id="b9641-450">事實上,每次調用該方法時,您都會使用新的編譯查詢條目來污染查詢計劃緩存。</span><span class="sxs-lookup"><span data-stu-id="b9641-450">In fact, you will be polluting your query plan cache with a new CompiledQuery entry every time the method is called.</span></span>

<span data-ttu-id="b9641-451">相反,您希望創建已編譯查詢的靜態實例,以便每次調用方法時都調用相同的已編譯查詢。</span><span class="sxs-lookup"><span data-stu-id="b9641-451">Instead, you want to create a static instance of the compiled query, so you are invoking the same compiled query every time the method is called.</span></span> <span data-ttu-id="b9641-452">這樣做的一種方法是添加編譯查詢實例作為物件上下文的成員。</span><span class="sxs-lookup"><span data-stu-id="b9641-452">One way to so this is by adding the CompiledQuery instance as a member of your object context.</span></span><span data-ttu-id="b9641-453">然後,您可以通過幫助器方法訪問編譯查詢,使事情變得更乾淨一些:</span><span class="sxs-lookup"><span data-stu-id="b9641-453">  You can then make things a little cleaner by accessing the CompiledQuery through a helper method:</span></span>

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

<span data-ttu-id="b9641-454">此說明器方法將呼叫如下:</span><span class="sxs-lookup"><span data-stu-id="b9641-454">This helper method would be invoked as follows:</span></span>

``` csharp
    this.productsGrid.DataSource = context.GetProductsForCategory(selectedCategory);
```

#### <a name="332-composing-over-a-compiledquery"></a><span data-ttu-id="b9641-455">3.3.2 通過編譯查詢進行群組</span><span class="sxs-lookup"><span data-stu-id="b9641-455">3.3.2       Composing over a CompiledQuery</span></span>

<span data-ttu-id="b9641-456">通過任何 LINQ 查詢進行撰寫的能力非常有用;為此,只需在*IQuery()* 或*Count()* 之後調用方法。</span><span class="sxs-lookup"><span data-stu-id="b9641-456">The ability to compose over any LINQ query is extremely useful; to do this, you simply invoke a method after the IQueryable such as *Skip()* or *Count()*.</span></span> <span data-ttu-id="b9641-457">但是,這樣做實質上會返回一個新的可查詢物件。</span><span class="sxs-lookup"><span data-stu-id="b9641-457">However, doing so essentially returns a new IQueryable object.</span></span> <span data-ttu-id="b9641-458">雖然在技術上沒有什麼可以阻止您通過 CompiledQuery 進行創作,但這樣做將導致生成需要再次通過計畫編譯器的新 IQuery 物件。</span><span class="sxs-lookup"><span data-stu-id="b9641-458">While there’s nothing to stop you technically from composing over a CompiledQuery, doing so will cause the generation of a new IQueryable object that requires passing through the plan compiler again.</span></span>

<span data-ttu-id="b9641-459">某些元件將使用組合的 IQuery 對象來實現進階功能。</span><span class="sxs-lookup"><span data-stu-id="b9641-459">Some components will make use of composed IQueryable objects to enable advanced functionality.</span></span> <span data-ttu-id="b9641-460">例如,ASP。NET 的 GridView 可以透過 SelectMethod 屬性將資料綁定到 IQuery 物件。</span><span class="sxs-lookup"><span data-stu-id="b9641-460">For example, ASP.NET’s GridView can be data-bound to an IQueryable object via the SelectMethod property.</span></span> <span data-ttu-id="b9641-461">然後,GridView 將在此可查詢物件上進行組合,以允許對數據模型進行排序和分頁。</span><span class="sxs-lookup"><span data-stu-id="b9641-461">The GridView will then compose over this IQueryable object to allow sorting and paging over the data model.</span></span> <span data-ttu-id="b9641-462">如您所見,使用 GridView 的編譯查詢不會命中已編譯的查詢,而是生成新的自動編譯查詢。</span><span class="sxs-lookup"><span data-stu-id="b9641-462">As you can see, using a CompiledQuery for the GridView would not hit the compiled query but would generate a new autocompiled query.</span></span>

<span data-ttu-id="b9641-463">在一個可能遇到這種情況的地方是向查詢添加累進篩選器時。</span><span class="sxs-lookup"><span data-stu-id="b9641-463">One place where you may run into this is when adding progressive filters to a query.</span></span> <span data-ttu-id="b9641-464">例如,假設您有一個客戶頁面,該頁面包含多個可選篩選器的下拉清單(例如,國家/地區訂單和訂單計數)。</span><span class="sxs-lookup"><span data-stu-id="b9641-464">For example, suppose you had a Customers page with several drop-down lists for optional filters (for example, Country and OrdersCount).</span></span> <span data-ttu-id="b9641-465">您可以在編譯查詢的 IQuery 可查詢結果上編寫這些篩選器,但這樣做將導致每次執行計畫編譯器時新查詢都會經過計畫編譯器。</span><span class="sxs-lookup"><span data-stu-id="b9641-465">You can compose these filters over the IQueryable results of a CompiledQuery, but doing so will result in the new query going through the plan compiler every time you execute it.</span></span>

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

 <span data-ttu-id="b9641-466">為了避免此重新編譯,您可以重寫編譯查詢,以考慮可能的篩選器:</span><span class="sxs-lookup"><span data-stu-id="b9641-466">To avoid this re-compilation, you can rewrite the CompiledQuery to take the possible filters into account:</span></span>

``` csharp
    private static readonly Func<NorthwindEntities, int, int?, string, IQueryable<Customer>> customersForEmployeeWithFiltersCQ = CompiledQuery.Compile(
        (NorthwindEntities context, int empId, int? countFilter, string countryFilter) =>
            context.Customers.Where(c => c.Orders.Any(o => o.EmployeeID == empId))
            .Where(c => countFilter.HasValue == false || c.Orders.Count > countFilter)
            .Where(c => countryFilter == null || c.Address.Country == countryFilter)
        );
```

<span data-ttu-id="b9641-467">將在 UI 中呼叫,例如:</span><span class="sxs-lookup"><span data-stu-id="b9641-467">Which would be invoked in the UI like:</span></span>

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

 <span data-ttu-id="b9641-468">此處的權衡是生成的儲存命令將始終具有帶有空檢查的篩選器,但對於資料庫伺服器來說,優化這些篩選器應該相當簡單:</span><span class="sxs-lookup"><span data-stu-id="b9641-468">A tradeoff here is the generated store command will always have the filters with the null checks, but these should be fairly simple for the database server to optimize:</span></span>

``` SQL
...
WHERE ((0 = (CASE WHEN (@p__linq__1 IS NOT NULL) THEN cast(1 as bit) WHEN (@p__linq__1 IS NULL) THEN cast(0 as bit) END)) OR ([Project3].[C2] > @p__linq__2)) AND (@p__linq__3 IS NULL OR [Project3].[Country] = @p__linq__4)
```

### <a name="34-metadata-caching"></a><span data-ttu-id="b9641-469">3.4 中繼資料快取</span><span class="sxs-lookup"><span data-stu-id="b9641-469">3.4 Metadata caching</span></span>

<span data-ttu-id="b9641-470">實體框架還支援元數據緩存。</span><span class="sxs-lookup"><span data-stu-id="b9641-470">The Entity Framework also supports Metadata caching.</span></span> <span data-ttu-id="b9641-471">這實質上是跨不同連接到同一模型的類型資訊和類型到資料庫映射資訊。</span><span class="sxs-lookup"><span data-stu-id="b9641-471">This is essentially caching of type information and type-to-database mapping information across different connections to the same model.</span></span> <span data-ttu-id="b9641-472">元數據緩存對於每個 AppDomain 是唯一的。</span><span class="sxs-lookup"><span data-stu-id="b9641-472">The Metadata cache is unique per AppDomain.</span></span>

#### <a name="341-metadata-caching-algorithm"></a><span data-ttu-id="b9641-473">3.4.1 中繼資料快取演算法</span><span class="sxs-lookup"><span data-stu-id="b9641-473">3.4.1 Metadata Caching algorithm</span></span>

1.  <span data-ttu-id="b9641-474">模型的元數據資訊存儲在每個實體連接的專案集合中。</span><span class="sxs-lookup"><span data-stu-id="b9641-474">Metadata information for a model is stored in an ItemCollection for each EntityConnection.</span></span>
    -   <span data-ttu-id="b9641-475">作為旁注,模型的不同部分有不同的 ItemCollection 物件。</span><span class="sxs-lookup"><span data-stu-id="b9641-475">As a side note, there are different ItemCollection objects for different parts of the model.</span></span> <span data-ttu-id="b9641-476">例如,StoreItem集合包含有關資料庫模型的資訊;因此,它包含有關資料庫模型的資訊。ObjectItemCollection 包含有關資料模型的資訊;EdmItemCollection 包含有關概念模型的資訊。</span><span class="sxs-lookup"><span data-stu-id="b9641-476">For example, StoreItemCollections contains the information about the database model; ObjectItemCollection contains information about the data model; EdmItemCollection contains information about the conceptual model.</span></span>

2.  <span data-ttu-id="b9641-477">如果兩個連接使用相同的連接字串,它們將共用相同的 ItemCollection 實例。</span><span class="sxs-lookup"><span data-stu-id="b9641-477">If two connections use the same connection string, they will share the same ItemCollection instance.</span></span>
3.  <span data-ttu-id="b9641-478">功能上等效,但文本上不同的連接字串可能會導致不同的元數據緩存。</span><span class="sxs-lookup"><span data-stu-id="b9641-478">Functionally equivalent but textually different connection strings may result in different metadata caches.</span></span> <span data-ttu-id="b9641-479">我們使用權杖化連接字串,因此只需更改權杖的順序即可生成共用元資料。</span><span class="sxs-lookup"><span data-stu-id="b9641-479">We do tokenize connection strings, so simply changing the order of the tokens should result in shared metadata.</span></span> <span data-ttu-id="b9641-480">但是,在標記化后,兩個在功能上看起來相同的連接字串可能不會被計算為相同。</span><span class="sxs-lookup"><span data-stu-id="b9641-480">But two connection strings that seem functionally the same may not be evaluated as identical after tokenization.</span></span>
4.  <span data-ttu-id="b9641-481">定期檢查專案集合以表示使用。</span><span class="sxs-lookup"><span data-stu-id="b9641-481">The ItemCollection is periodically checked for use.</span></span> <span data-ttu-id="b9641-482">如果確定最近未訪問工作區,則將在下一次緩存掃描時將其標記為清理。</span><span class="sxs-lookup"><span data-stu-id="b9641-482">If it is determined that a workspace has not been accessed recently, it will be marked for cleanup on the next cache sweep.</span></span>
5.  <span data-ttu-id="b9641-483">僅僅創建實體連接就會導致創建元數據緩存(儘管在打開連接之前不會初始化其中的項集合)。</span><span class="sxs-lookup"><span data-stu-id="b9641-483">Merely creating an EntityConnection will cause a metadata cache to be created (though the item collections in it will not be initialized until the connection is opened).</span></span> <span data-ttu-id="b9641-484">此工作區將保留在記憶體中,直到緩存演演算法確定它不"正在使用"。</span><span class="sxs-lookup"><span data-stu-id="b9641-484">This workspace will remain in-memory until the caching algorithm determines it is not “in use”.</span></span>

<span data-ttu-id="b9641-485">客戶諮詢團隊撰寫了一篇部落格文章,其中描述了對 ItemCollection 的引用,以避免在使用大型模型時「\<https://docs.microsoft.com/archive/blogs/appfabriccat/holding-a-reference-to-the-ef-metadataworkspace-for-wcf-services>棄用」: 。</span><span class="sxs-lookup"><span data-stu-id="b9641-485">The Customer Advisory Team has written a blog post that describes holding a reference to an ItemCollection in order to avoid "deprecation" when using large models: \<https://docs.microsoft.com/archive/blogs/appfabriccat/holding-a-reference-to-the-ef-metadataworkspace-for-wcf-services>.</span></span>

#### <a name="342-the-relationship-between-metadata-caching-and-query-plan-caching"></a><span data-ttu-id="b9641-486">3.4.2 元數據緩存和查詢計劃快取之間的關係</span><span class="sxs-lookup"><span data-stu-id="b9641-486">3.4.2 The relationship between Metadata Caching and Query Plan Caching</span></span>

<span data-ttu-id="b9641-487">查詢計劃緩存實例位於元數據工作區的存儲類型的專案集合中。</span><span class="sxs-lookup"><span data-stu-id="b9641-487">The query plan cache instance lives in the MetadataWorkspace's ItemCollection of store types.</span></span> <span data-ttu-id="b9641-488">這意味著快取儲存命令將用於查詢使用給定元資料工作區實例化的任何上下文。</span><span class="sxs-lookup"><span data-stu-id="b9641-488">This means that cached store commands will be used for queries against any context instantiated using a given MetadataWorkspace.</span></span> <span data-ttu-id="b9641-489">這也意味著,如果您有兩個連接字串略有不同,在標記后不匹配,則將具有不同的查詢計劃緩存實例。</span><span class="sxs-lookup"><span data-stu-id="b9641-489">It also means that if you have two connections strings that are slightly different and don't match after tokenizing, you will have different query plan cache instances.</span></span>

### <a name="35-results-caching"></a><span data-ttu-id="b9641-490">3.5 結果快取</span><span class="sxs-lookup"><span data-stu-id="b9641-490">3.5 Results caching</span></span>

<span data-ttu-id="b9641-491">使用結果快取(也稱為"第二級緩存"),您將查詢的結果保存在本地緩存中。</span><span class="sxs-lookup"><span data-stu-id="b9641-491">With results caching (also known as "second-level caching"), you keep the results of queries in a local cache.</span></span> <span data-ttu-id="b9641-492">發出查詢時,首先在查詢存儲之前,先查看結果是否在本地可用。</span><span class="sxs-lookup"><span data-stu-id="b9641-492">When issuing a query, you first see if the results are available locally before you query against the store.</span></span> <span data-ttu-id="b9641-493">雖然實體框架並不直接支持結果緩存,但可以使用包裝提供程式添加第二級緩存。</span><span class="sxs-lookup"><span data-stu-id="b9641-493">While results caching isn't directly supported by Entity Framework, it's possible to add a second level cache by using a wrapping provider.</span></span> <span data-ttu-id="b9641-494">以第二級快取的包裝提供者的範例是 Alachisoft[基於 NCache 的實體框架第二級緩存](https://www.alachisoft.com/ncache/entity-framework.html)。</span><span class="sxs-lookup"><span data-stu-id="b9641-494">An example wrapping provider with a second-level cache is Alachisoft's [Entity Framework Second Level Cache based on NCache](https://www.alachisoft.com/ncache/entity-framework.html).</span></span>

<span data-ttu-id="b9641-495">二級緩存的實現是在 LINQ 運算運算(和 fee)並從第一級緩存中計算或檢索查詢執行計畫之後發生的注入功能。</span><span class="sxs-lookup"><span data-stu-id="b9641-495">This implementation of second-level caching is an injected functionality that takes place after the LINQ expression has been evaluated (and funcletized) and the query execution plan is computed or retrieved from the first-level cache.</span></span> <span data-ttu-id="b9641-496">然後,第二級緩存將僅存儲原始資料庫結果,因此具體化管道在之後仍執行。</span><span class="sxs-lookup"><span data-stu-id="b9641-496">The second-level cache will then store only the raw database results, so the materialization pipeline still executes afterwards.</span></span>

#### <a name="351-additional-references-for-results-caching-with-the-wrapping-provider"></a><span data-ttu-id="b9641-497">3.5.1 使用包裝提供程式進行結果緩存的其他引用</span><span class="sxs-lookup"><span data-stu-id="b9641-497">3.5.1 Additional references for results caching with the wrapping provider</span></span>

-   <span data-ttu-id="b9641-498">Julie Lerman 撰寫了一篇「實體框架和 Windows Azure 中的二級緩存」MSDN 文章,其中包括如何更新範例包裝提供者以使用 Windows Server AppFabric 快取:[https://msdn.microsoft.com/magazine/hh394143.aspx](https://msdn.microsoft.com/magazine/hh394143.aspx)</span><span class="sxs-lookup"><span data-stu-id="b9641-498">Julie Lerman has written a "Second-Level Caching in Entity Framework and Windows Azure" MSDN article that includes how to update the sample wrapping provider to use Windows Server AppFabric caching: [https://msdn.microsoft.com/magazine/hh394143.aspx](https://msdn.microsoft.com/magazine/hh394143.aspx)</span></span>
-   <span data-ttu-id="b9641-499">如果您正在使用實體框架 5,則團隊部落格有一篇文章,說明如何使用實體框架 5 的快取\<https://docs.microsoft.com/archive/blogs/adonet/ef-caching-with-jarek-kowalskis-provider>提供程式執行內容: 。</span><span class="sxs-lookup"><span data-stu-id="b9641-499">If you are working with Entity Framework 5, the team blog has a post that describes how to get things running with the caching provider for Entity Framework 5: \<https://docs.microsoft.com/archive/blogs/adonet/ef-caching-with-jarek-kowalskis-provider>.</span></span> <span data-ttu-id="b9641-500">它還包括 T4 範本,以幫助自動將二級緩存添加到專案中。</span><span class="sxs-lookup"><span data-stu-id="b9641-500">It also includes a T4 template to help automate adding the 2nd-level caching to your project.</span></span>

## <a name="4-autocompiled-queries"></a><span data-ttu-id="b9641-501">4 自動編譯查詢</span><span class="sxs-lookup"><span data-stu-id="b9641-501">4 Autocompiled Queries</span></span>

<span data-ttu-id="b9641-502">使用實體框架對資料庫發出查詢時,它必須經過一系列步驟才能實際實現結果;其中一個步驟是查詢編譯。</span><span class="sxs-lookup"><span data-stu-id="b9641-502">When a query is issued against a database using Entity Framework, it must go through a series of steps before actually materializing the results; one such step is Query Compilation.</span></span> <span data-ttu-id="b9641-503">實體 SQL 查詢在自動緩存時具有良好的性能,因此,在進行同一查詢的第二次或第三次時,它可以跳過計劃編譯器並使用緩存的計劃。</span><span class="sxs-lookup"><span data-stu-id="b9641-503">Entity SQL queries were known to have good performance as they are automatically cached, so the second or third time you execute the same query it can skip the plan compiler and use the cached plan instead.</span></span>

<span data-ttu-id="b9641-504">實體框架 5 還引入了 LINQ 到實體查詢的自動緩存。</span><span class="sxs-lookup"><span data-stu-id="b9641-504">Entity Framework 5 introduced automatic caching for LINQ to Entities queries as well.</span></span> <span data-ttu-id="b9641-505">在以往版本的實體框架中,創建編譯查詢以加快性能是一種常見做法,因為這將使 LINQ 到實體查詢可緩存。</span><span class="sxs-lookup"><span data-stu-id="b9641-505">In past editions of Entity Framework creating a CompiledQuery to speed your performance was a common practice, as this would make your LINQ to Entities query cacheable.</span></span> <span data-ttu-id="b9641-506">由於緩存現在無需使用編譯查詢即可自動完成,因此我們將此功能稱為"自動編譯查詢"。</span><span class="sxs-lookup"><span data-stu-id="b9641-506">Since caching is now done automatically without the use of a CompiledQuery, we call this feature “autocompiled queries”.</span></span> <span data-ttu-id="b9641-507">有關查詢計劃緩存及其機制的詳細資訊,請參閱查詢計劃緩存。</span><span class="sxs-lookup"><span data-stu-id="b9641-507">For more information about the query plan cache and its mechanics, see Query Plan Caching.</span></span>

<span data-ttu-id="b9641-508">實體框架檢測查詢何時需要重新編譯,並且在調用查詢時(即使以前已編譯)也執行。</span><span class="sxs-lookup"><span data-stu-id="b9641-508">Entity Framework detects when a query requires to be recompiled, and does so when the query is invoked even if it had been compiled before.</span></span> <span data-ttu-id="b9641-509">導致重新編譯查詢的常見條件是:</span><span class="sxs-lookup"><span data-stu-id="b9641-509">Common conditions that cause the query to be recompiled are:</span></span>

-   <span data-ttu-id="b9641-510">更改與您的查詢關聯的合併選項。</span><span class="sxs-lookup"><span data-stu-id="b9641-510">Changing the MergeOption associated to your query.</span></span> <span data-ttu-id="b9641-511">將不會使用緩存的查詢,而是計劃編譯器將再次運行,並緩存新創建的計劃。</span><span class="sxs-lookup"><span data-stu-id="b9641-511">The cached query will not be used, instead the plan compiler will run again and the newly created plan gets cached.</span></span>
-   <span data-ttu-id="b9641-512">更改上下文選項的值。</span><span class="sxs-lookup"><span data-stu-id="b9641-512">Changing the value of ContextOptions.UseCSharpNullComparisonBehavior.</span></span> <span data-ttu-id="b9641-513">您將獲得與更改合併選項相同的效果。</span><span class="sxs-lookup"><span data-stu-id="b9641-513">You get the same effect as changing the MergeOption.</span></span>

<span data-ttu-id="b9641-514">其他條件可能會阻止查詢使用緩存。</span><span class="sxs-lookup"><span data-stu-id="b9641-514">Other conditions can prevent your query from using the cache.</span></span> <span data-ttu-id="b9641-515">常見範例包括：</span><span class="sxs-lookup"><span data-stu-id="b9641-515">Common examples are:</span></span>

-   <span data-ttu-id="b9641-516">使用 IE500T&lt;&gt;。包含&lt;&gt;(T 值)。</span><span class="sxs-lookup"><span data-stu-id="b9641-516">Using IEnumerable&lt;T&gt;.Contains&lt;&gt;(T value).</span></span>
-   <span data-ttu-id="b9641-517">使用生成具有常量的查詢的函數。</span><span class="sxs-lookup"><span data-stu-id="b9641-517">Using functions that produce queries with constants.</span></span>
-   <span data-ttu-id="b9641-518">使用非映射對象的屬性。</span><span class="sxs-lookup"><span data-stu-id="b9641-518">Using the properties of a non-mapped object.</span></span>
-   <span data-ttu-id="b9641-519">將查詢連結到需要重新編譯的另一個查詢。</span><span class="sxs-lookup"><span data-stu-id="b9641-519">Linking your query to another query that requires to be recompiled.</span></span>

### <a name="41-using-ienumerablelttgtcontainslttgtt-value"></a><span data-ttu-id="b9641-520">4.1 使用 IE500T&lt;。&gt;包含&lt;T(T&gt;值)</span><span class="sxs-lookup"><span data-stu-id="b9641-520">4.1 Using IEnumerable&lt;T&gt;.Contains&lt;T&gt;(T value)</span></span>

<span data-ttu-id="b9641-521">實體框架不緩存調用&lt;IE55T&gt;的 查詢。包含&lt;針對記憶體&gt;中集合的 T(T 值),因為集合的值被視為易失性。</span><span class="sxs-lookup"><span data-stu-id="b9641-521">Entity Framework does not cache queries that invoke IEnumerable&lt;T&gt;.Contains&lt;T&gt;(T value) against an in-memory collection, since the values of the collection are considered volatile.</span></span> <span data-ttu-id="b9641-522">以下範例查詢將不會緩存,因此計劃編譯器將始終對其進行處理:</span><span class="sxs-lookup"><span data-stu-id="b9641-522">The following example query will not be cached, so it will always be processed by the plan compiler:</span></span>

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

<span data-ttu-id="b9641-523">請注意,執行「包含」的 IE55 的大小的確定查詢的編譯速度或速度。</span><span class="sxs-lookup"><span data-stu-id="b9641-523">Note that the size of the IEnumerable against which Contains is executed determines how fast or how slow your query is compiled.</span></span> <span data-ttu-id="b9641-524">使用大型集合(如上例中所示的集合)時,性能可能會受到顯著影響。</span><span class="sxs-lookup"><span data-stu-id="b9641-524">Performance can suffer significantly when using large collections such as the one shown in the example above.</span></span>

<span data-ttu-id="b9641-525">實體框架 6 包含對 IE50t&lt;T&gt;方式的優化。在執行查詢&lt;&gt;時,包含 T(T 值)工作。</span><span class="sxs-lookup"><span data-stu-id="b9641-525">Entity Framework 6 contains optimizations to the way IEnumerable&lt;T&gt;.Contains&lt;T&gt;(T value) works when queries are executed.</span></span> <span data-ttu-id="b9641-526">生成的 SQL 代碼生成速度要快得多,而且可讀性更強,在大多數情況下,它還在伺服器中執行得更快。</span><span class="sxs-lookup"><span data-stu-id="b9641-526">The SQL code that is generated is much faster to produce and more readable, and in most cases it also executes faster in the server.</span></span>

### <a name="42-using-functions-that-produce-queries-with-constants"></a><span data-ttu-id="b9641-527">4.2 使用產生具有常量的查詢的函數</span><span class="sxs-lookup"><span data-stu-id="b9641-527">4.2 Using functions that produce queries with constants</span></span>

<span data-ttu-id="b9641-528">Skip()、Take()、包含()和 DefautIfEmpty() LINQ 運算符不生成具有參數的 SQL 查詢,而是將傳遞給它們的值作為常量傳遞給它們。</span><span class="sxs-lookup"><span data-stu-id="b9641-528">The Skip(), Take(), Contains() and DefautIfEmpty() LINQ operators do not produce SQL queries with parameters but instead put the values passed to them as constants.</span></span> <span data-ttu-id="b9641-529">因此,否則可能相同的查詢最終會污染 EF 堆疊和資料庫伺服器上的查詢計劃緩存,除非在後續查詢執行中使用相同的常量,否則不會重新使用。</span><span class="sxs-lookup"><span data-stu-id="b9641-529">Because of this, queries that might otherwise be identical end up polluting the query plan cache, both on the EF stack and on the database server, and do not get reutilized unless the same constants are used in a subsequent query execution.</span></span> <span data-ttu-id="b9641-530">例如：</span><span class="sxs-lookup"><span data-stu-id="b9641-530">For example:</span></span>

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

<span data-ttu-id="b9641-531">在此示例中,每次執行此查詢時,使用 ID 的不同值,查詢將編譯為新計劃。</span><span class="sxs-lookup"><span data-stu-id="b9641-531">In this example, each time this query is executed with a different value for id the query will be compiled into a new plan.</span></span>

<span data-ttu-id="b9641-532">在進行分頁時,尤其要注意使用跳過和取。</span><span class="sxs-lookup"><span data-stu-id="b9641-532">In particular pay attention to the use of Skip and Take when doing paging.</span></span> <span data-ttu-id="b9641-533">在 EF6 中,這些方法具有 lambda 重載,可有效地使緩存的查詢計劃可重用,因為 EF 可以擷取傳遞給這些方法的變數並將其轉換為 SQL 參數。</span><span class="sxs-lookup"><span data-stu-id="b9641-533">In EF6 these methods have a lambda overload that effectively makes the cached query plan reusable because EF can capture variables passed to these methods and translate them to SQLparameters.</span></span> <span data-ttu-id="b9641-534">這也有助於保持緩存更乾淨,否則每個查詢使用不同的常量為 Skip 和 Take 將獲得其自己的查詢計畫緩存條目。</span><span class="sxs-lookup"><span data-stu-id="b9641-534">This also helps keep the cache cleaner since otherwise each query with a different constant for Skip and Take would get its own query plan cache entry.</span></span>

<span data-ttu-id="b9641-535">請考慮以下代碼,該代碼不理想,但僅用於舉例說明此類查詢:</span><span class="sxs-lookup"><span data-stu-id="b9641-535">Consider the following code, which is suboptimal but is only meant to exemplify this class of queries:</span></span>

``` csharp
var customers = context.Customers.OrderBy(c => c.LastName);
for (var i = 0; i < count; ++i)
{
    var currentCustomer = customers.Skip(i).FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

<span data-ttu-id="b9641-536">此代碼的更快版本將涉及呼叫使用 lambda 的跳過:</span><span class="sxs-lookup"><span data-stu-id="b9641-536">A faster version of this same code would involve calling Skip with a lambda:</span></span>

``` csharp
var customers = context.Customers.OrderBy(c => c.LastName);
for (var i = 0; i < count; ++i)
{
    var currentCustomer = customers.Skip(() => i).FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

<span data-ttu-id="b9641-537">第二個代碼段的運行速度可能加快 11%,因為每次運行查詢時都使用相同的查詢計劃,從而節省了 CPU 時間並避免污染查詢緩存。</span><span class="sxs-lookup"><span data-stu-id="b9641-537">The second snippet may run up to 11% faster because the same query plan is used every time the query is run, which saves CPU time and avoids polluting the query cache.</span></span> <span data-ttu-id="b9641-538">此外,由於 Skip 的參數位於閉包中,因此代碼現在可能如下所示:</span><span class="sxs-lookup"><span data-stu-id="b9641-538">Furthermore, because the parameter to Skip is in a closure the code might as well look like this now:</span></span>

``` csharp
var i = 0;
var skippyCustomers = context.Customers.OrderBy(c => c.LastName).Skip(() => i);
for (; i < count; ++i)
{
    var currentCustomer = skippyCustomers.FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

### <a name="43-using-the-properties-of-a-non-mapped-object"></a><span data-ttu-id="b9641-539">4.3 使用非映射物件屬性</span><span class="sxs-lookup"><span data-stu-id="b9641-539">4.3 Using the properties of a non-mapped object</span></span>

<span data-ttu-id="b9641-540">當查詢使用非映射物件類型的屬性作為參數時,查詢將不會被緩存。</span><span class="sxs-lookup"><span data-stu-id="b9641-540">When a query uses the properties of a non-mapped object type as a parameter then the query will not get cached.</span></span> <span data-ttu-id="b9641-541">例如：</span><span class="sxs-lookup"><span data-stu-id="b9641-541">For example:</span></span>

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

<span data-ttu-id="b9641-542">在此示例中,假設類非映射類型不是實體模型的一部分。</span><span class="sxs-lookup"><span data-stu-id="b9641-542">In this example, assume that class NonMappedType is not part of the Entity model.</span></span> <span data-ttu-id="b9641-543">可以輕鬆地更改此查詢,以不使用非映射類型,而是使用本地變數作為查詢的參數:</span><span class="sxs-lookup"><span data-stu-id="b9641-543">This query can easily be changed to not use a non-mapped type and instead use a local variable as the parameter to the query:</span></span>

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

<span data-ttu-id="b9641-544">在這種情況下,查詢將能夠獲得緩存,並從查詢計劃緩存中獲益。</span><span class="sxs-lookup"><span data-stu-id="b9641-544">In this case, the query will be able to get cached and will benefit from the query plan cache.</span></span>

### <a name="44-linking-to-queries-that-require-recompiling"></a><span data-ttu-id="b9641-545">4.4 連結到需要重新編譯的查詢</span><span class="sxs-lookup"><span data-stu-id="b9641-545">4.4 Linking to queries that require recompiling</span></span>

<span data-ttu-id="b9641-546">按照上述相同的示例,如果您有第二個依賴於需要重新編譯的查詢,則整個第二個查詢也將重新編譯。</span><span class="sxs-lookup"><span data-stu-id="b9641-546">Following the same example as above, if you have a second query that relies on a query that needs to be recompiled, your entire second query will also be recompiled.</span></span> <span data-ttu-id="b9641-547">下面是一個範例來說明此方案:</span><span class="sxs-lookup"><span data-stu-id="b9641-547">Here’s an example to illustrate this scenario:</span></span>

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

<span data-ttu-id="b9641-548">該示例是泛型的,但它說明了連結到第一查詢如何導致第二查詢無法緩存。</span><span class="sxs-lookup"><span data-stu-id="b9641-548">The example is generic, but it illustrates how linking to firstQuery is causing secondQuery to be unable to get cached.</span></span> <span data-ttu-id="b9641-549">如果第一查詢不是需要重新編譯的查詢,則第二查詢將被緩存。</span><span class="sxs-lookup"><span data-stu-id="b9641-549">If firstQuery had not been a query that requires recompiling, then secondQuery would have been cached.</span></span>

## <a name="5-notracking-queries"></a><span data-ttu-id="b9641-550">5 無追蹤查詢</span><span class="sxs-lookup"><span data-stu-id="b9641-550">5 NoTracking Queries</span></span>

### <a name="51-disabling-change-tracking-to-reduce-state-management-overhead"></a><span data-ttu-id="b9641-551">5.1 停用變更追蹤以減少狀態管理花費</span><span class="sxs-lookup"><span data-stu-id="b9641-551">5.1 Disabling change tracking to reduce state management overhead</span></span>

<span data-ttu-id="b9641-552">如果您處於唯讀方案,並且希望避免將物件載入ObjectStateManager中的開銷,則可以發出「無追蹤」查詢。</span><span class="sxs-lookup"><span data-stu-id="b9641-552">If you are in a read-only scenario and want to avoid the overhead of loading the objects into the ObjectStateManager, you can issue "No Tracking" queries.</span></span><span data-ttu-id="b9641-553">可以在查詢級別禁用更改跟蹤。</span><span class="sxs-lookup"><span data-stu-id="b9641-553">  Change tracking can be disabled at the query level.</span></span>

<span data-ttu-id="b9641-554">但請注意,通過禁用更改跟蹤,您實際上正在關閉物件緩存。</span><span class="sxs-lookup"><span data-stu-id="b9641-554">Note though that by disabling change tracking you are effectively turning off the object cache.</span></span> <span data-ttu-id="b9641-555">當您查詢實體時,我們不能通過從ObjectStateManager 提取以前具體化的查詢結果來跳過具體化。</span><span class="sxs-lookup"><span data-stu-id="b9641-555">When you query for an entity, we can't skip materialization by pulling the previously-materialized query results from the ObjectStateManager.</span></span> <span data-ttu-id="b9641-556">如果在同一上下文中反覆查詢相同的實體,則實際上可能會看到啟用更改跟蹤的性能優勢。</span><span class="sxs-lookup"><span data-stu-id="b9641-556">If you are repeatedly querying for the same entities on the same context, you might actually see a performance benefit from enabling change tracking.</span></span>

<span data-ttu-id="b9641-557">使用 ObjectContext 查詢時,ObjectQuery 和 ObjectSet 實例將在設定合併選項後記住它,並且在它們上組成的查詢將繼承父查詢的有效 MergeOption。</span><span class="sxs-lookup"><span data-stu-id="b9641-557">When querying using ObjectContext, ObjectQuery and ObjectSet instances will remember a MergeOption once it is set, and queries that are composed on them will inherit the effective MergeOption of the parent query.</span></span> <span data-ttu-id="b9641-558">使用 DbContext 時,可以通過在 DbSet 上調用 AsNo 追蹤() 修改器來禁用追蹤。</span><span class="sxs-lookup"><span data-stu-id="b9641-558">When using DbContext, tracking can be disabled by calling the AsNoTracking() modifier on the DbSet.</span></span>

#### <a name="511-disabling-change-tracking-for-a-query-when-using-dbcontext"></a><span data-ttu-id="b9641-559">5.1.1 禁用使用 DbContext 時查詢的變更追蹤</span><span class="sxs-lookup"><span data-stu-id="b9641-559">5.1.1 Disabling change tracking for a query when using DbContext</span></span>

<span data-ttu-id="b9641-560">通過將調用連結到查詢中的 AsNoTrack() 方法,可以將查詢模式切換到 NoTrack。</span><span class="sxs-lookup"><span data-stu-id="b9641-560">You can switch the mode of a query to NoTracking by chaining a call to the AsNoTracking() method in the query.</span></span> <span data-ttu-id="b9641-561">與物件查詢不同,DbContext API 中的 DbSet 和 DbQuery 類沒有 MergeOption 的可變屬性。</span><span class="sxs-lookup"><span data-stu-id="b9641-561">Unlike ObjectQuery, the DbSet and DbQuery classes in the DbContext API don’t have a mutable property for the MergeOption.</span></span>

``` csharp
    var productsForCategory = from p in context.Products.AsNoTracking()
                                where p.Category.CategoryName == selectedCategory
                                select p;


```

#### <a name="512-disabling-change-tracking-at-the-query-level-using-objectcontext"></a><span data-ttu-id="b9641-562">5.1.2 使用 ObjectContext 關閉查詢等級的變更追蹤</span><span class="sxs-lookup"><span data-stu-id="b9641-562">5.1.2 Disabling change tracking at the query level using ObjectContext</span></span>

``` csharp
    var productsForCategory = from p in context.Products
                                where p.Category.CategoryName == selectedCategory
                                select p;

    ((ObjectQuery)productsForCategory).MergeOption = MergeOption.NoTracking;
```

#### <a name="513-disabling-change-tracking-for-an-entire-entity-set-using-objectcontext"></a><span data-ttu-id="b9641-563">5.1.3 使用 ObjectContext 關閉整個實體集的變更追蹤</span><span class="sxs-lookup"><span data-stu-id="b9641-563">5.1.3 Disabling change tracking for an entire entity set using ObjectContext</span></span>

``` csharp
    context.Products.MergeOption = MergeOption.NoTracking;

    var productsForCategory = from p in context.Products
                                where p.Category.CategoryName == selectedCategory
                                select p;
```

### <a name="52test-metrics-demonstrating-the-performance-benefit-of-notracking-queries"></a><span data-ttu-id="b9641-564">5.2 測試指標,演示 NoTrack 查詢的性能優勢</span><span class="sxs-lookup"><span data-stu-id="b9641-564">5.2 Test Metrics demonstrating the performance benefit of NoTracking queries</span></span>

<span data-ttu-id="b9641-565">在此測試中,我們將透過將追蹤與 Navision 模型的 NoTrack 查詢進行比較來查看填充 Object StateManager 的成本。</span><span class="sxs-lookup"><span data-stu-id="b9641-565">In this test we look at the cost of filling the ObjectStateManager by comparing Tracking to NoTracking queries for the Navision model.</span></span> <span data-ttu-id="b9641-566">有關 Navision 模型的說明以及已執行的查詢類型,請參閱附錄。</span><span class="sxs-lookup"><span data-stu-id="b9641-566">See the appendix for a description of the Navision model and the types of queries which were executed.</span></span> <span data-ttu-id="b9641-567">在此測試中,我們反覆運算查詢清單並執行每個查詢一次。</span><span class="sxs-lookup"><span data-stu-id="b9641-567">In this test, we iterate through the list of queries and execute each one once.</span></span> <span data-ttu-id="b9641-568">我們運行了兩個測試變體,一次使用 NoTrack 查詢,另一次使用默認合併選項"僅追加"。</span><span class="sxs-lookup"><span data-stu-id="b9641-568">We ran two variations of the test, once with NoTracking queries and once with the default merge option of "AppendOnly".</span></span> <span data-ttu-id="b9641-569">我們運行每個變體 3 次,並獲取運行的平均值。</span><span class="sxs-lookup"><span data-stu-id="b9641-569">We ran each variation 3 times and take the mean value of the runs.</span></span> <span data-ttu-id="b9641-570">在測試之間,我們清除 SQL Server 上的查詢快取,並透過執行以下指令來收縮 tempdb:</span><span class="sxs-lookup"><span data-stu-id="b9641-570">Between the tests we clear the query cache on the SQL Server and shrink the tempdb by running the following commands:</span></span>

1.  <span data-ttu-id="b9641-571">DBCC DROPCLEANBUFFERS</span><span class="sxs-lookup"><span data-stu-id="b9641-571">DBCC DROPCLEANBUFFERS</span></span>
2.  <span data-ttu-id="b9641-572">DBCC FREEPROCCACHE</span><span class="sxs-lookup"><span data-stu-id="b9641-572">DBCC FREEPROCCACHE</span></span>
3.  <span data-ttu-id="b9641-573">DBCC SHRINK 資料庫(tempdb, 0)</span><span class="sxs-lookup"><span data-stu-id="b9641-573">DBCC SHRINKDATABASE (tempdb, 0)</span></span>

<span data-ttu-id="b9641-574">測試結果,3 次以上的中值:</span><span class="sxs-lookup"><span data-stu-id="b9641-574">Test Results, median over 3 runs:</span></span>

|                        | <span data-ttu-id="b9641-575">無追蹤 + 工作集</span><span class="sxs-lookup"><span data-stu-id="b9641-575">NO TRACKING – WORKING SET</span></span> | <span data-ttu-id="b9641-576">無追蹤 + 時間</span><span class="sxs-lookup"><span data-stu-id="b9641-576">NO TRACKING – TIME</span></span> | <span data-ttu-id="b9641-577">只附加 = 工作集</span><span class="sxs-lookup"><span data-stu-id="b9641-577">APPEND ONLY – WORKING SET</span></span> | <span data-ttu-id="b9641-578">只附加 = 時間</span><span class="sxs-lookup"><span data-stu-id="b9641-578">APPEND ONLY – TIME</span></span> |
|:-----------------------|:--------------------------|:-------------------|:--------------------------|:-------------------|
| <span data-ttu-id="b9641-579">**實體框架 5**</span><span class="sxs-lookup"><span data-stu-id="b9641-579">**Entity Framework 5**</span></span> | <span data-ttu-id="b9641-580">460361728</span><span class="sxs-lookup"><span data-stu-id="b9641-580">460361728</span></span>                 | <span data-ttu-id="b9641-581">1163536 毫秒</span><span class="sxs-lookup"><span data-stu-id="b9641-581">1163536 ms</span></span>         | <span data-ttu-id="b9641-582">596545536</span><span class="sxs-lookup"><span data-stu-id="b9641-582">596545536</span></span>                 | <span data-ttu-id="b9641-583">1273042 毫秒</span><span class="sxs-lookup"><span data-stu-id="b9641-583">1273042 ms</span></span>         |
| <span data-ttu-id="b9641-584">**Entity Framework 6**</span><span class="sxs-lookup"><span data-stu-id="b9641-584">**Entity Framework 6**</span></span> | <span data-ttu-id="b9641-585">647127040</span><span class="sxs-lookup"><span data-stu-id="b9641-585">647127040</span></span>                 | <span data-ttu-id="b9641-586">190228 毫秒</span><span class="sxs-lookup"><span data-stu-id="b9641-586">190228 ms</span></span>          | <span data-ttu-id="b9641-587">832798720</span><span class="sxs-lookup"><span data-stu-id="b9641-587">832798720</span></span>                 | <span data-ttu-id="b9641-588">195521 毫秒</span><span class="sxs-lookup"><span data-stu-id="b9641-588">195521 ms</span></span>          |

<span data-ttu-id="b9641-589">實體框架 5 在運行結束時的記憶體佔用量將比實體框架 6 少。</span><span class="sxs-lookup"><span data-stu-id="b9641-589">Entity Framework 5 will have a smaller memory footprint at the end of the run than Entity Framework 6 does.</span></span> <span data-ttu-id="b9641-590">實體框架 6 使用的額外記憶體是啟用新功能和更好性能的額外記憶體結構和代碼的結果。</span><span class="sxs-lookup"><span data-stu-id="b9641-590">The additional memory consumed by Entity Framework 6 is the result of additional memory structures and code that enable new features and better performance.</span></span>

<span data-ttu-id="b9641-591">使用 Object StateManager 時,記憶體佔用空間也有明顯差異。</span><span class="sxs-lookup"><span data-stu-id="b9641-591">There’s also a clear difference in memory footprint when using the ObjectStateManager.</span></span> <span data-ttu-id="b9641-592">實體框架 5 在追蹤我們從資料庫中實現的所有實體時,其佔用空間增加了 30%。</span><span class="sxs-lookup"><span data-stu-id="b9641-592">Entity Framework 5 increased its footprint by 30% when keeping track of all the entities we materialized from the database.</span></span> <span data-ttu-id="b9641-593">實體框架 6 這樣做時佔用空間增加了 28%。</span><span class="sxs-lookup"><span data-stu-id="b9641-593">Entity Framework 6 increased its footprint by 28% when doing so.</span></span>

<span data-ttu-id="b9641-594">在時間方面,實體框架 6 在此測試中以較大優勢優於實體框架 5。</span><span class="sxs-lookup"><span data-stu-id="b9641-594">In terms of time, Entity Framework 6 outperforms Entity Framework 5 in this test by a large margin.</span></span> <span data-ttu-id="b9641-595">實體框架 6 在實體框架 5 消耗的時間大約 16% 的時間內完成了測試。</span><span class="sxs-lookup"><span data-stu-id="b9641-595">Entity Framework 6 completed the test in roughly 16% of the time consumed by Entity Framework 5.</span></span> <span data-ttu-id="b9641-596">此外,使用ObjectStateManager時,實體框架5需要9%的時間才能完成。</span><span class="sxs-lookup"><span data-stu-id="b9641-596">Additionally, Entity Framework 5 takes 9% more time to complete when the ObjectStateManager is being used.</span></span> <span data-ttu-id="b9641-597">相比之下,實體框架 6 在使用 Object StateManager 時使用的時間要長 3%。</span><span class="sxs-lookup"><span data-stu-id="b9641-597">In comparison, Entity Framework 6 is using 3% more time when using the ObjectStateManager.</span></span>

## <a name="6-query-execution-options"></a><span data-ttu-id="b9641-598">6 查詢執行選項</span><span class="sxs-lookup"><span data-stu-id="b9641-598">6 Query Execution Options</span></span>

<span data-ttu-id="b9641-599">實體框架提供了幾種不同的查詢方法。</span><span class="sxs-lookup"><span data-stu-id="b9641-599">Entity Framework offers several different ways to query.</span></span> <span data-ttu-id="b9641-600">我們將介紹以下選項,比較每個選項的優缺點,並檢查它們的性能特徵:</span><span class="sxs-lookup"><span data-stu-id="b9641-600">We'll take a look at the following options, compare the pros and cons of each, and examine their performance characteristics:</span></span>

-   <span data-ttu-id="b9641-601">林Q 到實體。</span><span class="sxs-lookup"><span data-stu-id="b9641-601">LINQ to Entities.</span></span>
-   <span data-ttu-id="b9641-602">沒有跟蹤 LINQ 到實體。</span><span class="sxs-lookup"><span data-stu-id="b9641-602">No Tracking LINQ to Entities.</span></span>
-   <span data-ttu-id="b9641-603">物件查詢的實體 SQL。</span><span class="sxs-lookup"><span data-stu-id="b9641-603">Entity SQL over an ObjectQuery.</span></span>
-   <span data-ttu-id="b9641-604">實體命令的實體 SQL。</span><span class="sxs-lookup"><span data-stu-id="b9641-604">Entity SQL over an EntityCommand.</span></span>
-   <span data-ttu-id="b9641-605">執行存儲查詢。</span><span class="sxs-lookup"><span data-stu-id="b9641-605">ExecuteStoreQuery.</span></span>
-   <span data-ttu-id="b9641-606">SqlQuery。</span><span class="sxs-lookup"><span data-stu-id="b9641-606">SqlQuery.</span></span>
-   <span data-ttu-id="b9641-607">編譯查詢。</span><span class="sxs-lookup"><span data-stu-id="b9641-607">CompiledQuery.</span></span>

### <a name="61-linq-to-entities-queries"></a><span data-ttu-id="b9641-608">6.1 LINQ 到實體查詢</span><span class="sxs-lookup"><span data-stu-id="b9641-608">6.1       LINQ to Entities queries</span></span>

``` csharp
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
```

<span data-ttu-id="b9641-609">**優點**</span><span class="sxs-lookup"><span data-stu-id="b9641-609">**Pros**</span></span>

-   <span data-ttu-id="b9641-610">適用於 CUD 操作。</span><span class="sxs-lookup"><span data-stu-id="b9641-610">Suitable for CUD operations.</span></span>
-   <span data-ttu-id="b9641-611">完全具體化的物件。</span><span class="sxs-lookup"><span data-stu-id="b9641-611">Fully materialized objects.</span></span>
-   <span data-ttu-id="b9641-612">最簡單的編寫語法內置於程式設計語言中。</span><span class="sxs-lookup"><span data-stu-id="b9641-612">Simplest to write with syntax built into the programming language.</span></span>
-   <span data-ttu-id="b9641-613">性能好。</span><span class="sxs-lookup"><span data-stu-id="b9641-613">Good performance.</span></span>

<span data-ttu-id="b9641-614">**缺點**</span><span class="sxs-lookup"><span data-stu-id="b9641-614">**Cons**</span></span>

-   <span data-ttu-id="b9641-615">某些技術限制,例如:</span><span class="sxs-lookup"><span data-stu-id="b9641-615">Certain technical restrictions, such as:</span></span>
    -   <span data-ttu-id="b9641-616">對外部 JOIN 查詢使用預設 IfEmpty 的模式會導致比實體 SQL 中簡單的外部 JOIN 語句更複雜的查詢。</span><span class="sxs-lookup"><span data-stu-id="b9641-616">Patterns using DefaultIfEmpty for OUTER JOIN queries result in more complex queries than simple OUTER JOIN statements in Entity SQL.</span></span>
    -   <span data-ttu-id="b9641-617">您仍然不能將 LIKE 用於常規模式匹配。</span><span class="sxs-lookup"><span data-stu-id="b9641-617">You still can’t use LIKE with general pattern matching.</span></span>

### <a name="62-no-tracking-linq-to-entities-queries"></a><span data-ttu-id="b9641-618">6.2 沒有追蹤 LINQ 到實體查詢</span><span class="sxs-lookup"><span data-stu-id="b9641-618">6.2       No Tracking LINQ to Entities queries</span></span>

<span data-ttu-id="b9641-619">當上下文派生物件上下文時:</span><span class="sxs-lookup"><span data-stu-id="b9641-619">When the context derives ObjectContext:</span></span>

``` csharp
context.Products.MergeOption = MergeOption.NoTracking;
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
```

<span data-ttu-id="b9641-620">當上下文派生 DbContext 時:</span><span class="sxs-lookup"><span data-stu-id="b9641-620">When the context derives DbContext:</span></span>

``` csharp
var q = context.Products.AsNoTracking()
                        .Where(p => p.Category.CategoryName == "Beverages");
```

<span data-ttu-id="b9641-621">**優點**</span><span class="sxs-lookup"><span data-stu-id="b9641-621">**Pros**</span></span>

-   <span data-ttu-id="b9641-622">與常規 LINQ 查詢性能提高。</span><span class="sxs-lookup"><span data-stu-id="b9641-622">Improved performance over regular LINQ queries.</span></span>
-   <span data-ttu-id="b9641-623">完全具體化的物件。</span><span class="sxs-lookup"><span data-stu-id="b9641-623">Fully materialized objects.</span></span>
-   <span data-ttu-id="b9641-624">最簡單的編寫語法內置於程式設計語言中。</span><span class="sxs-lookup"><span data-stu-id="b9641-624">Simplest to write with syntax built into the programming language.</span></span>

<span data-ttu-id="b9641-625">**缺點**</span><span class="sxs-lookup"><span data-stu-id="b9641-625">**Cons**</span></span>

-   <span data-ttu-id="b9641-626">不適合 CUD 操作。</span><span class="sxs-lookup"><span data-stu-id="b9641-626">Not suitable for CUD operations.</span></span>
-   <span data-ttu-id="b9641-627">某些技術限制,例如:</span><span class="sxs-lookup"><span data-stu-id="b9641-627">Certain technical restrictions, such as:</span></span>
    -   <span data-ttu-id="b9641-628">對外部 JOIN 查詢使用預設 IfEmpty 的模式會導致比實體 SQL 中簡單的外部 JOIN 語句更複雜的查詢。</span><span class="sxs-lookup"><span data-stu-id="b9641-628">Patterns using DefaultIfEmpty for OUTER JOIN queries result in more complex queries than simple OUTER JOIN statements in Entity SQL.</span></span>
    -   <span data-ttu-id="b9641-629">您仍然不能將 LIKE 用於常規模式匹配。</span><span class="sxs-lookup"><span data-stu-id="b9641-629">You still can’t use LIKE with general pattern matching.</span></span>

<span data-ttu-id="b9641-630">請注意,即使未指定 NoTracking,也不會追蹤專案標量屬性的查詢。</span><span class="sxs-lookup"><span data-stu-id="b9641-630">Note that queries that project scalar properties are not tracked even if the NoTracking is not specified.</span></span> <span data-ttu-id="b9641-631">例如：</span><span class="sxs-lookup"><span data-stu-id="b9641-631">For example:</span></span>

``` csharp
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages").Select(p => new { p.ProductName });
```

<span data-ttu-id="b9641-632">此特定查詢未顯式指定為 NoTracking,但由於它未具體化物件狀態管理器已知的類型,因此不會跟蹤具體化結果。</span><span class="sxs-lookup"><span data-stu-id="b9641-632">This particular query doesn’t explicitly specify being NoTracking, but since it’s not materializing a type that’s known to the object state manager then the materialized result is not tracked.</span></span>

### <a name="63-entity-sql-over-an-objectquery"></a><span data-ttu-id="b9641-633">6.3 物件查詢的實體 SQL</span><span class="sxs-lookup"><span data-stu-id="b9641-633">6.3       Entity SQL over an ObjectQuery</span></span>

``` csharp
ObjectQuery<Product> products = context.Products.Where("it.Category.CategoryName = 'Beverages'");
```

<span data-ttu-id="b9641-634">**優點**</span><span class="sxs-lookup"><span data-stu-id="b9641-634">**Pros**</span></span>

-   <span data-ttu-id="b9641-635">適用於 CUD 操作。</span><span class="sxs-lookup"><span data-stu-id="b9641-635">Suitable for CUD operations.</span></span>
-   <span data-ttu-id="b9641-636">完全具體化的物件。</span><span class="sxs-lookup"><span data-stu-id="b9641-636">Fully materialized objects.</span></span>
-   <span data-ttu-id="b9641-637">支持查詢計劃緩存。</span><span class="sxs-lookup"><span data-stu-id="b9641-637">Supports query plan caching.</span></span>

<span data-ttu-id="b9641-638">**缺點**</span><span class="sxs-lookup"><span data-stu-id="b9641-638">**Cons**</span></span>

-   <span data-ttu-id="b9641-639">涉及文本查詢字串,與內置於語言中的查詢構造相比,這些字串更容易出現使用者錯誤。</span><span class="sxs-lookup"><span data-stu-id="b9641-639">Involves textual query strings which are more prone to user error than query constructs built into the language.</span></span>

### <a name="64-entity-sql-over-an-entity-command"></a><span data-ttu-id="b9641-640">6.4 實體命令的實體 SQL</span><span class="sxs-lookup"><span data-stu-id="b9641-640">6.4       Entity SQL over an Entity Command</span></span>

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

<span data-ttu-id="b9641-641">**優點**</span><span class="sxs-lookup"><span data-stu-id="b9641-641">**Pros**</span></span>

-   <span data-ttu-id="b9641-642">支援 .NET 4.0 中的查詢計劃快取(.NET 4.5 中的所有其他查詢類型都支援計劃緩存)。</span><span class="sxs-lookup"><span data-stu-id="b9641-642">Supports query plan caching in .NET 4.0 (plan caching is supported by all other query types in .NET 4.5).</span></span>

<span data-ttu-id="b9641-643">**缺點**</span><span class="sxs-lookup"><span data-stu-id="b9641-643">**Cons**</span></span>

-   <span data-ttu-id="b9641-644">涉及文本查詢字串,與內置於語言中的查詢構造相比,這些字串更容易出現使用者錯誤。</span><span class="sxs-lookup"><span data-stu-id="b9641-644">Involves textual query strings which are more prone to user error than query constructs built into the language.</span></span>
-   <span data-ttu-id="b9641-645">不適合 CUD 操作。</span><span class="sxs-lookup"><span data-stu-id="b9641-645">Not suitable for CUD operations.</span></span>
-   <span data-ttu-id="b9641-646">結果不會自動具體化,必須從數據讀取器讀取。</span><span class="sxs-lookup"><span data-stu-id="b9641-646">Results are not automatically materialized, and must be read from the data reader.</span></span>

### <a name="65-sqlquery-and-executestorequery"></a><span data-ttu-id="b9641-647">6.5 SqlQuery 和執行儲存查詢</span><span class="sxs-lookup"><span data-stu-id="b9641-647">6.5       SqlQuery and ExecuteStoreQuery</span></span>

<span data-ttu-id="b9641-648">資料庫上的 SqlQuery:</span><span class="sxs-lookup"><span data-stu-id="b9641-648">SqlQuery on Database:</span></span>

``` csharp
// use this to obtain entities and not track them
var q1 = context.Database.SqlQuery<Product>("select * from products");
```

<span data-ttu-id="b9641-649">DbSet 上的 SqlQuery:</span><span class="sxs-lookup"><span data-stu-id="b9641-649">SqlQuery on DbSet:</span></span>

``` csharp
// use this to obtain entities and have them tracked
var q2 = context.Products.SqlQuery("select * from products");
```

<span data-ttu-id="b9641-650">ExecyteStore查詢:</span><span class="sxs-lookup"><span data-stu-id="b9641-650">ExecyteStoreQuery:</span></span>

``` csharp
var beverages = context.ExecuteStoreQuery<Product>(
@"     SELECT        P.ProductID, P.ProductName, P.SupplierID, P.CategoryID, P.QuantityPerUnit, P.UnitPrice, P.UnitsInStock, P.UnitsOnOrder, P.ReorderLevel, P.Discontinued, P.DiscontinuedDate
       FROM            Products AS P INNER JOIN Categories AS C ON P.CategoryID = C.CategoryID
       WHERE        (C.CategoryName = 'Beverages')"
);
```

<span data-ttu-id="b9641-651">**優點**</span><span class="sxs-lookup"><span data-stu-id="b9641-651">**Pros**</span></span>

-   <span data-ttu-id="b9641-652">通常性能最快,因為計劃編譯器是繞過的。</span><span class="sxs-lookup"><span data-stu-id="b9641-652">Generally fastest performance since plan compiler is bypassed.</span></span>
-   <span data-ttu-id="b9641-653">完全具體化的物件。</span><span class="sxs-lookup"><span data-stu-id="b9641-653">Fully materialized objects.</span></span>
-   <span data-ttu-id="b9641-654">適用於從 DbSet 使用的 CUD 操作。</span><span class="sxs-lookup"><span data-stu-id="b9641-654">Suitable for CUD operations when used from the DbSet.</span></span>

<span data-ttu-id="b9641-655">**缺點**</span><span class="sxs-lookup"><span data-stu-id="b9641-655">**Cons**</span></span>

-   <span data-ttu-id="b9641-656">查詢是文本和容易出錯的。</span><span class="sxs-lookup"><span data-stu-id="b9641-656">Query is textual and error prone.</span></span>
-   <span data-ttu-id="b9641-657">通過使用存儲語義而不是概念語義,查詢綁定到特定的後端。</span><span class="sxs-lookup"><span data-stu-id="b9641-657">Query is tied to a specific backend by using store semantics instead of conceptual semantics.</span></span>
-   <span data-ttu-id="b9641-658">存在繼承時,手工製作的查詢需要考慮所請求類型的映射條件。</span><span class="sxs-lookup"><span data-stu-id="b9641-658">When inheritance is present, handcrafted query needs to account for mapping conditions for the type requested.</span></span>

### <a name="66-compiledquery"></a><span data-ttu-id="b9641-659">6.6 編譯查詢</span><span class="sxs-lookup"><span data-stu-id="b9641-659">6.6       CompiledQuery</span></span>

``` csharp
private static readonly Func<NorthwindEntities, string, IQueryable<Product>> productsForCategoryCQ = CompiledQuery.Compile(
    (NorthwindEntities context, string categoryName) =>
        context.Products.Where(p => p.Category.CategoryName == categoryName)
        );
…
var q = context.InvokeProductsForCategoryCQ("Beverages");
```

<span data-ttu-id="b9641-660">**優點**</span><span class="sxs-lookup"><span data-stu-id="b9641-660">**Pros**</span></span>

-   <span data-ttu-id="b9641-661">與常規 LINQ 查詢(LINQ 查詢)提供高達 7% 的性能改進。</span><span class="sxs-lookup"><span data-stu-id="b9641-661">Provides up to a 7% performance improvement over regular LINQ queries.</span></span>
-   <span data-ttu-id="b9641-662">完全具體化的物件。</span><span class="sxs-lookup"><span data-stu-id="b9641-662">Fully materialized objects.</span></span>
-   <span data-ttu-id="b9641-663">適用於 CUD 操作。</span><span class="sxs-lookup"><span data-stu-id="b9641-663">Suitable for CUD operations.</span></span>

<span data-ttu-id="b9641-664">**缺點**</span><span class="sxs-lookup"><span data-stu-id="b9641-664">**Cons**</span></span>

-   <span data-ttu-id="b9641-665">增加了複雜性和程式設計開銷。</span><span class="sxs-lookup"><span data-stu-id="b9641-665">Increased complexity and programming overhead.</span></span>
-   <span data-ttu-id="b9641-666">在編譯的查詢上作曲時,性能改進將丟失。</span><span class="sxs-lookup"><span data-stu-id="b9641-666">The performance improvement is lost when composing on top of a compiled query.</span></span>
-   <span data-ttu-id="b9641-667">某些 LINQ 查詢不能編寫為編譯查詢 - 例如,匿名類型的投影。</span><span class="sxs-lookup"><span data-stu-id="b9641-667">Some LINQ queries can't be written as a CompiledQuery - for example, projections of anonymous types.</span></span>

### <a name="67-performance-comparison-of-different-query-options"></a><span data-ttu-id="b9641-668">6.7 不同查詢選項的效能比較</span><span class="sxs-lookup"><span data-stu-id="b9641-668">6.7       Performance Comparison of different query options</span></span>

<span data-ttu-id="b9641-669">在未進行時間創建時的簡單微觀基準測試已過。</span><span class="sxs-lookup"><span data-stu-id="b9641-669">Simple microbenchmarks where the context creation was not timed were put to the test.</span></span> <span data-ttu-id="b9641-670">我們在受控環境中對一組非緩存實體進行了 5000 次查詢。</span><span class="sxs-lookup"><span data-stu-id="b9641-670">We measured querying 5000 times for a set of non-cached entities in a controlled environment.</span></span> <span data-ttu-id="b9641-671">這些數位應該帶有警告:它們並不反映應用程式產生的實際數位,而是非常準確地測量了在比較 Apple 到 apple 的不同查詢選項時存在的性能差異量,不包括創建新上下文的成本。</span><span class="sxs-lookup"><span data-stu-id="b9641-671">These numbers are to be taken with a warning: they do not reflect actual numbers produced by an application, but instead they are a very accurate measurement of how much of a performance difference there is when different querying options are compared apples-to-apples, excluding the cost of creating a new context.</span></span>

| <span data-ttu-id="b9641-672">EF</span><span class="sxs-lookup"><span data-stu-id="b9641-672">EF</span></span>  | <span data-ttu-id="b9641-673">測試</span><span class="sxs-lookup"><span data-stu-id="b9641-673">Test</span></span>                                 | <span data-ttu-id="b9641-674">時間 (毫秒)</span><span class="sxs-lookup"><span data-stu-id="b9641-674">Time (ms)</span></span> | <span data-ttu-id="b9641-675">記憶體</span><span class="sxs-lookup"><span data-stu-id="b9641-675">Memory</span></span>   |
|:----|:-------------------------------------|:----------|:---------|
| <span data-ttu-id="b9641-676">EF5</span><span class="sxs-lookup"><span data-stu-id="b9641-676">EF5</span></span> | <span data-ttu-id="b9641-677">物件上下文 ESQL</span><span class="sxs-lookup"><span data-stu-id="b9641-677">ObjectContext ESQL</span></span>                   | <span data-ttu-id="b9641-678">2414</span><span class="sxs-lookup"><span data-stu-id="b9641-678">2414</span></span>      | <span data-ttu-id="b9641-679">38801408</span><span class="sxs-lookup"><span data-stu-id="b9641-679">38801408</span></span> |
| <span data-ttu-id="b9641-680">EF5</span><span class="sxs-lookup"><span data-stu-id="b9641-680">EF5</span></span> | <span data-ttu-id="b9641-681">物件內容查詢</span><span class="sxs-lookup"><span data-stu-id="b9641-681">ObjectContext Linq Query</span></span>             | <span data-ttu-id="b9641-682">2692</span><span class="sxs-lookup"><span data-stu-id="b9641-682">2692</span></span>      | <span data-ttu-id="b9641-683">38277120</span><span class="sxs-lookup"><span data-stu-id="b9641-683">38277120</span></span> |
| <span data-ttu-id="b9641-684">EF5</span><span class="sxs-lookup"><span data-stu-id="b9641-684">EF5</span></span> | <span data-ttu-id="b9641-685">資料庫內容庫內容查詢無追蹤</span><span class="sxs-lookup"><span data-stu-id="b9641-685">DbContext Linq Query No Tracking</span></span>     | <span data-ttu-id="b9641-686">2818</span><span class="sxs-lookup"><span data-stu-id="b9641-686">2818</span></span>      | <span data-ttu-id="b9641-687">41840640</span><span class="sxs-lookup"><span data-stu-id="b9641-687">41840640</span></span> |
| <span data-ttu-id="b9641-688">EF5</span><span class="sxs-lookup"><span data-stu-id="b9641-688">EF5</span></span> | <span data-ttu-id="b9641-689">資料庫上下文林克查詢</span><span class="sxs-lookup"><span data-stu-id="b9641-689">DbContext Linq Query</span></span>                 | <span data-ttu-id="b9641-690">2930</span><span class="sxs-lookup"><span data-stu-id="b9641-690">2930</span></span>      | <span data-ttu-id="b9641-691">41771008</span><span class="sxs-lookup"><span data-stu-id="b9641-691">41771008</span></span> |
| <span data-ttu-id="b9641-692">EF5</span><span class="sxs-lookup"><span data-stu-id="b9641-692">EF5</span></span> | <span data-ttu-id="b9641-693">物件內容查詢無追蹤</span><span class="sxs-lookup"><span data-stu-id="b9641-693">ObjectContext Linq Query No Tracking</span></span> | <span data-ttu-id="b9641-694">3013</span><span class="sxs-lookup"><span data-stu-id="b9641-694">3013</span></span>      | <span data-ttu-id="b9641-695">38412288</span><span class="sxs-lookup"><span data-stu-id="b9641-695">38412288</span></span> |
|     |                                      |           |          |
| <span data-ttu-id="b9641-696">EF6</span><span class="sxs-lookup"><span data-stu-id="b9641-696">EF6</span></span> | <span data-ttu-id="b9641-697">物件上下文 ESQL</span><span class="sxs-lookup"><span data-stu-id="b9641-697">ObjectContext ESQL</span></span>                   | <span data-ttu-id="b9641-698">2059</span><span class="sxs-lookup"><span data-stu-id="b9641-698">2059</span></span>      | <span data-ttu-id="b9641-699">46039040</span><span class="sxs-lookup"><span data-stu-id="b9641-699">46039040</span></span> |
| <span data-ttu-id="b9641-700">EF6</span><span class="sxs-lookup"><span data-stu-id="b9641-700">EF6</span></span> | <span data-ttu-id="b9641-701">物件內容查詢</span><span class="sxs-lookup"><span data-stu-id="b9641-701">ObjectContext Linq Query</span></span>             | <span data-ttu-id="b9641-702">3074</span><span class="sxs-lookup"><span data-stu-id="b9641-702">3074</span></span>      | <span data-ttu-id="b9641-703">45248512</span><span class="sxs-lookup"><span data-stu-id="b9641-703">45248512</span></span> |
| <span data-ttu-id="b9641-704">EF6</span><span class="sxs-lookup"><span data-stu-id="b9641-704">EF6</span></span> | <span data-ttu-id="b9641-705">資料庫內容庫內容查詢無追蹤</span><span class="sxs-lookup"><span data-stu-id="b9641-705">DbContext Linq Query No Tracking</span></span>     | <span data-ttu-id="b9641-706">3125</span><span class="sxs-lookup"><span data-stu-id="b9641-706">3125</span></span>      | <span data-ttu-id="b9641-707">47575040</span><span class="sxs-lookup"><span data-stu-id="b9641-707">47575040</span></span> |
| <span data-ttu-id="b9641-708">EF6</span><span class="sxs-lookup"><span data-stu-id="b9641-708">EF6</span></span> | <span data-ttu-id="b9641-709">資料庫上下文林克查詢</span><span class="sxs-lookup"><span data-stu-id="b9641-709">DbContext Linq Query</span></span>                 | <span data-ttu-id="b9641-710">3420</span><span class="sxs-lookup"><span data-stu-id="b9641-710">3420</span></span>      | <span data-ttu-id="b9641-711">47652864</span><span class="sxs-lookup"><span data-stu-id="b9641-711">47652864</span></span> |
| <span data-ttu-id="b9641-712">EF6</span><span class="sxs-lookup"><span data-stu-id="b9641-712">EF6</span></span> | <span data-ttu-id="b9641-713">物件內容查詢無追蹤</span><span class="sxs-lookup"><span data-stu-id="b9641-713">ObjectContext Linq Query No Tracking</span></span> | <span data-ttu-id="b9641-714">3593</span><span class="sxs-lookup"><span data-stu-id="b9641-714">3593</span></span>      | <span data-ttu-id="b9641-715">45260800</span><span class="sxs-lookup"><span data-stu-id="b9641-715">45260800</span></span> |

![EF5 微型基準,5000 個暖疊代](~/ef6/media/ef5micro5000warm.png)

![EF6 微型基準,5000 個暖疊代](~/ef6/media/ef6micro5000warm.png)

<span data-ttu-id="b9641-718">微基準對代碼中的小更改非常敏感。</span><span class="sxs-lookup"><span data-stu-id="b9641-718">Microbenchmarks are very sensitive to small changes in the code.</span></span> <span data-ttu-id="b9641-719">在這種情況下,實體框架 5 和實體框架 6 的成本之間的差額是由於[增加了攔截](~/ef6/fundamentals/logging-and-interception.md)和[事務性改進](~/ef6/saving/transactions.md)。</span><span class="sxs-lookup"><span data-stu-id="b9641-719">In this case, the difference between the costs of Entity Framework 5 and Entity Framework 6 are due to the addition of [interception](~/ef6/fundamentals/logging-and-interception.md) and [transactional improvements](~/ef6/saving/transactions.md).</span></span> <span data-ttu-id="b9641-720">然而,這些微觀基準數位是實體框架所做操作的一個很小的碎片中放大的視野。</span><span class="sxs-lookup"><span data-stu-id="b9641-720">These microbenchmarks numbers, however, are an amplified vision into a very small fragment of what Entity Framework does.</span></span> <span data-ttu-id="b9641-721">從實體框架 5 升級到實體框架 6 時,暖查詢的實際方案不應看到性能回歸。</span><span class="sxs-lookup"><span data-stu-id="b9641-721">Real-world scenarios of warm queries should not see a performance regression when upgrading from Entity Framework 5 to Entity Framework 6.</span></span>

<span data-ttu-id="b9641-722">為了比較不同查詢選項的實際性能,我們創建了 5 個單獨的測試變體,其中我們使用不同的查詢選項來選擇類別名稱為"飲料"的所有產品。</span><span class="sxs-lookup"><span data-stu-id="b9641-722">To compare the real-world performance of the different query options, we created 5 separate test variations where we use a different query option to select all products whose category name is "Beverages".</span></span> <span data-ttu-id="b9641-723">每個反覆運算包括創建上下文的成本,以及實現所有返回的實體的成本。</span><span class="sxs-lookup"><span data-stu-id="b9641-723">Each iteration includes the cost of creating the context, and the cost of materializing all returned entities.</span></span> <span data-ttu-id="b9641-724">在採用 1000 個有時算反覆運算的總和之前,將不及時運行 10 個反覆運算。</span><span class="sxs-lookup"><span data-stu-id="b9641-724">10 iterations are run untimed before taking the sum of 1000 timed iterations.</span></span> <span data-ttu-id="b9641-725">顯示的結果是從每次測試的 5 次運行中獲取的中位運行。</span><span class="sxs-lookup"><span data-stu-id="b9641-725">The results shown are the median run taken from 5 runs of each test.</span></span> <span data-ttu-id="b9641-726">有關詳細資訊,請參閱附錄 B,其中包含測試的代碼。</span><span class="sxs-lookup"><span data-stu-id="b9641-726">For more information, see Appendix B which includes the code for the test.</span></span>

| <span data-ttu-id="b9641-727">EF</span><span class="sxs-lookup"><span data-stu-id="b9641-727">EF</span></span>  | <span data-ttu-id="b9641-728">測試</span><span class="sxs-lookup"><span data-stu-id="b9641-728">Test</span></span>                                        | <span data-ttu-id="b9641-729">時間 (毫秒)</span><span class="sxs-lookup"><span data-stu-id="b9641-729">Time (ms)</span></span> | <span data-ttu-id="b9641-730">記憶體</span><span class="sxs-lookup"><span data-stu-id="b9641-730">Memory</span></span>   |
|:----|:--------------------------------------------|:----------|:---------|
| <span data-ttu-id="b9641-731">EF5</span><span class="sxs-lookup"><span data-stu-id="b9641-731">EF5</span></span> | <span data-ttu-id="b9641-732">物件內容文實體命令</span><span class="sxs-lookup"><span data-stu-id="b9641-732">ObjectContext Entity Command</span></span>                | <span data-ttu-id="b9641-733">621</span><span class="sxs-lookup"><span data-stu-id="b9641-733">621</span></span>       | <span data-ttu-id="b9641-734">39350272</span><span class="sxs-lookup"><span data-stu-id="b9641-734">39350272</span></span> |
| <span data-ttu-id="b9641-735">EF5</span><span class="sxs-lookup"><span data-stu-id="b9641-735">EF5</span></span> | <span data-ttu-id="b9641-736">資料庫上的 DbContext Sql 查詢</span><span class="sxs-lookup"><span data-stu-id="b9641-736">DbContext Sql Query on Database</span></span>             | <span data-ttu-id="b9641-737">825</span><span class="sxs-lookup"><span data-stu-id="b9641-737">825</span></span>       | <span data-ttu-id="b9641-738">37519360</span><span class="sxs-lookup"><span data-stu-id="b9641-738">37519360</span></span> |
| <span data-ttu-id="b9641-739">EF5</span><span class="sxs-lookup"><span data-stu-id="b9641-739">EF5</span></span> | <span data-ttu-id="b9641-740">物件內容儲存查詢</span><span class="sxs-lookup"><span data-stu-id="b9641-740">ObjectContext Store Query</span></span>                   | <span data-ttu-id="b9641-741">878</span><span class="sxs-lookup"><span data-stu-id="b9641-741">878</span></span>       | <span data-ttu-id="b9641-742">39460864</span><span class="sxs-lookup"><span data-stu-id="b9641-742">39460864</span></span> |
| <span data-ttu-id="b9641-743">EF5</span><span class="sxs-lookup"><span data-stu-id="b9641-743">EF5</span></span> | <span data-ttu-id="b9641-744">物件內容查詢無追蹤</span><span class="sxs-lookup"><span data-stu-id="b9641-744">ObjectContext Linq Query No Tracking</span></span>        | <span data-ttu-id="b9641-745">969</span><span class="sxs-lookup"><span data-stu-id="b9641-745">969</span></span>       | <span data-ttu-id="b9641-746">38293504</span><span class="sxs-lookup"><span data-stu-id="b9641-746">38293504</span></span> |
| <span data-ttu-id="b9641-747">EF5</span><span class="sxs-lookup"><span data-stu-id="b9641-747">EF5</span></span> | <span data-ttu-id="b9641-748">使用物件查詢的物件內容內容的文字實體 Sql</span><span class="sxs-lookup"><span data-stu-id="b9641-748">ObjectContext Entity Sql using Object Query</span></span> | <span data-ttu-id="b9641-749">1089</span><span class="sxs-lookup"><span data-stu-id="b9641-749">1089</span></span>      | <span data-ttu-id="b9641-750">38981632</span><span class="sxs-lookup"><span data-stu-id="b9641-750">38981632</span></span> |
| <span data-ttu-id="b9641-751">EF5</span><span class="sxs-lookup"><span data-stu-id="b9641-751">EF5</span></span> | <span data-ttu-id="b9641-752">物件內容名稱文編譯查詢</span><span class="sxs-lookup"><span data-stu-id="b9641-752">ObjectContext Compiled Query</span></span>                | <span data-ttu-id="b9641-753">1099</span><span class="sxs-lookup"><span data-stu-id="b9641-753">1099</span></span>      | <span data-ttu-id="b9641-754">38682624</span><span class="sxs-lookup"><span data-stu-id="b9641-754">38682624</span></span> |
| <span data-ttu-id="b9641-755">EF5</span><span class="sxs-lookup"><span data-stu-id="b9641-755">EF5</span></span> | <span data-ttu-id="b9641-756">物件內容查詢</span><span class="sxs-lookup"><span data-stu-id="b9641-756">ObjectContext Linq Query</span></span>                    | <span data-ttu-id="b9641-757">1152</span><span class="sxs-lookup"><span data-stu-id="b9641-757">1152</span></span>      | <span data-ttu-id="b9641-758">38178816</span><span class="sxs-lookup"><span data-stu-id="b9641-758">38178816</span></span> |
| <span data-ttu-id="b9641-759">EF5</span><span class="sxs-lookup"><span data-stu-id="b9641-759">EF5</span></span> | <span data-ttu-id="b9641-760">資料庫內容庫內容查詢無追蹤</span><span class="sxs-lookup"><span data-stu-id="b9641-760">DbContext Linq Query No Tracking</span></span>            | <span data-ttu-id="b9641-761">1208</span><span class="sxs-lookup"><span data-stu-id="b9641-761">1208</span></span>      | <span data-ttu-id="b9641-762">41803776</span><span class="sxs-lookup"><span data-stu-id="b9641-762">41803776</span></span> |
| <span data-ttu-id="b9641-763">EF5</span><span class="sxs-lookup"><span data-stu-id="b9641-763">EF5</span></span> | <span data-ttu-id="b9641-764">DbSet 上的資料庫內容的資料庫內容</span><span class="sxs-lookup"><span data-stu-id="b9641-764">DbContext Sql Query on DbSet</span></span>                | <span data-ttu-id="b9641-765">1414</span><span class="sxs-lookup"><span data-stu-id="b9641-765">1414</span></span>      | <span data-ttu-id="b9641-766">37982208</span><span class="sxs-lookup"><span data-stu-id="b9641-766">37982208</span></span> |
| <span data-ttu-id="b9641-767">EF5</span><span class="sxs-lookup"><span data-stu-id="b9641-767">EF5</span></span> | <span data-ttu-id="b9641-768">資料庫上下文林克查詢</span><span class="sxs-lookup"><span data-stu-id="b9641-768">DbContext Linq Query</span></span>                        | <span data-ttu-id="b9641-769">1574</span><span class="sxs-lookup"><span data-stu-id="b9641-769">1574</span></span>      | <span data-ttu-id="b9641-770">41738240</span><span class="sxs-lookup"><span data-stu-id="b9641-770">41738240</span></span> |
|     |                                             |           |          |
| <span data-ttu-id="b9641-771">EF6</span><span class="sxs-lookup"><span data-stu-id="b9641-771">EF6</span></span> | <span data-ttu-id="b9641-772">物件內容文實體命令</span><span class="sxs-lookup"><span data-stu-id="b9641-772">ObjectContext Entity Command</span></span>                | <span data-ttu-id="b9641-773">480</span><span class="sxs-lookup"><span data-stu-id="b9641-773">480</span></span>       | <span data-ttu-id="b9641-774">47247360</span><span class="sxs-lookup"><span data-stu-id="b9641-774">47247360</span></span> |
| <span data-ttu-id="b9641-775">EF6</span><span class="sxs-lookup"><span data-stu-id="b9641-775">EF6</span></span> | <span data-ttu-id="b9641-776">物件內容儲存查詢</span><span class="sxs-lookup"><span data-stu-id="b9641-776">ObjectContext Store Query</span></span>                   | <span data-ttu-id="b9641-777">493</span><span class="sxs-lookup"><span data-stu-id="b9641-777">493</span></span>       | <span data-ttu-id="b9641-778">46739456</span><span class="sxs-lookup"><span data-stu-id="b9641-778">46739456</span></span> |
| <span data-ttu-id="b9641-779">EF6</span><span class="sxs-lookup"><span data-stu-id="b9641-779">EF6</span></span> | <span data-ttu-id="b9641-780">資料庫上的 DbContext Sql 查詢</span><span class="sxs-lookup"><span data-stu-id="b9641-780">DbContext Sql Query on Database</span></span>             | <span data-ttu-id="b9641-781">614</span><span class="sxs-lookup"><span data-stu-id="b9641-781">614</span></span>       | <span data-ttu-id="b9641-782">41607168</span><span class="sxs-lookup"><span data-stu-id="b9641-782">41607168</span></span> |
| <span data-ttu-id="b9641-783">EF6</span><span class="sxs-lookup"><span data-stu-id="b9641-783">EF6</span></span> | <span data-ttu-id="b9641-784">物件內容查詢無追蹤</span><span class="sxs-lookup"><span data-stu-id="b9641-784">ObjectContext Linq Query No Tracking</span></span>        | <span data-ttu-id="b9641-785">684</span><span class="sxs-lookup"><span data-stu-id="b9641-785">684</span></span>       | <span data-ttu-id="b9641-786">46333952</span><span class="sxs-lookup"><span data-stu-id="b9641-786">46333952</span></span> |
| <span data-ttu-id="b9641-787">EF6</span><span class="sxs-lookup"><span data-stu-id="b9641-787">EF6</span></span> | <span data-ttu-id="b9641-788">使用物件查詢的物件內容內容的文字實體 Sql</span><span class="sxs-lookup"><span data-stu-id="b9641-788">ObjectContext Entity Sql using Object Query</span></span> | <span data-ttu-id="b9641-789">767</span><span class="sxs-lookup"><span data-stu-id="b9641-789">767</span></span>       | <span data-ttu-id="b9641-790">48865280</span><span class="sxs-lookup"><span data-stu-id="b9641-790">48865280</span></span> |
| <span data-ttu-id="b9641-791">EF6</span><span class="sxs-lookup"><span data-stu-id="b9641-791">EF6</span></span> | <span data-ttu-id="b9641-792">物件內容名稱文編譯查詢</span><span class="sxs-lookup"><span data-stu-id="b9641-792">ObjectContext Compiled Query</span></span>                | <span data-ttu-id="b9641-793">788</span><span class="sxs-lookup"><span data-stu-id="b9641-793">788</span></span>       | <span data-ttu-id="b9641-794">48467968</span><span class="sxs-lookup"><span data-stu-id="b9641-794">48467968</span></span> |
| <span data-ttu-id="b9641-795">EF6</span><span class="sxs-lookup"><span data-stu-id="b9641-795">EF6</span></span> | <span data-ttu-id="b9641-796">資料庫內容庫內容查詢無追蹤</span><span class="sxs-lookup"><span data-stu-id="b9641-796">DbContext Linq Query No Tracking</span></span>            | <span data-ttu-id="b9641-797">878</span><span class="sxs-lookup"><span data-stu-id="b9641-797">878</span></span>       | <span data-ttu-id="b9641-798">47554560</span><span class="sxs-lookup"><span data-stu-id="b9641-798">47554560</span></span> |
| <span data-ttu-id="b9641-799">EF6</span><span class="sxs-lookup"><span data-stu-id="b9641-799">EF6</span></span> | <span data-ttu-id="b9641-800">物件內容查詢</span><span class="sxs-lookup"><span data-stu-id="b9641-800">ObjectContext Linq Query</span></span>                    | <span data-ttu-id="b9641-801">953</span><span class="sxs-lookup"><span data-stu-id="b9641-801">953</span></span>       | <span data-ttu-id="b9641-802">47632384</span><span class="sxs-lookup"><span data-stu-id="b9641-802">47632384</span></span> |
| <span data-ttu-id="b9641-803">EF6</span><span class="sxs-lookup"><span data-stu-id="b9641-803">EF6</span></span> | <span data-ttu-id="b9641-804">DbSet 上的資料庫內容的資料庫內容</span><span class="sxs-lookup"><span data-stu-id="b9641-804">DbContext Sql Query on DbSet</span></span>                | <span data-ttu-id="b9641-805">1023</span><span class="sxs-lookup"><span data-stu-id="b9641-805">1023</span></span>      | <span data-ttu-id="b9641-806">41992192</span><span class="sxs-lookup"><span data-stu-id="b9641-806">41992192</span></span> |
| <span data-ttu-id="b9641-807">EF6</span><span class="sxs-lookup"><span data-stu-id="b9641-807">EF6</span></span> | <span data-ttu-id="b9641-808">資料庫上下文林克查詢</span><span class="sxs-lookup"><span data-stu-id="b9641-808">DbContext Linq Query</span></span>                        | <span data-ttu-id="b9641-809">1290</span><span class="sxs-lookup"><span data-stu-id="b9641-809">1290</span></span>      | <span data-ttu-id="b9641-810">47529984</span><span class="sxs-lookup"><span data-stu-id="b9641-810">47529984</span></span> |


![EF5 暖查詢 1000 次反覆運算](~/ef6/media/ef5warmquery1000.png)

![EF6 暖查詢 1000 次反覆運算](~/ef6/media/ef6warmquery1000.png)

> [!NOTE]
> <span data-ttu-id="b9641-813">為完整,我們包括一個變體,其中我們在實體命令上執行實體 SQL 查詢。</span><span class="sxs-lookup"><span data-stu-id="b9641-813">For completeness, we included a variation where we execute an Entity SQL query on an EntityCommand.</span></span> <span data-ttu-id="b9641-814">但是,由於此類查詢的結果未具體化,因此比較不一定是蘋果到蘋果。</span><span class="sxs-lookup"><span data-stu-id="b9641-814">However, because results are not materialized for such queries, the comparison isn't necessarily apples-to-apples.</span></span> <span data-ttu-id="b9641-815">測試包括接近具體化,試圖使比較更公平。</span><span class="sxs-lookup"><span data-stu-id="b9641-815">The test includes a close approximation to materializing to try making the comparison fairer.</span></span>

<span data-ttu-id="b9641-816">在此端到端案例中,實體框架 6 優於實體框架 5,因為對堆疊的幾個部分進行了性能改進,包括更輕的 DbContext 初始化和&lt;更快&gt;的中數據收集 T 尋找。</span><span class="sxs-lookup"><span data-stu-id="b9641-816">In this end-to-end case, Entity Framework 6 outperforms Entity Framework 5 due to performance improvements made on several parts of the stack, including a much lighter DbContext initialization and faster MetadataCollection&lt;T&gt; lookups.</span></span>

## <a name="7-design-time-performance-considerations"></a><span data-ttu-id="b9641-817">7 設計時間效能注意事項</span><span class="sxs-lookup"><span data-stu-id="b9641-817">7 Design time performance considerations</span></span>

### <a name="71-inheritance-strategies"></a><span data-ttu-id="b9641-818">7.1 繼承原則</span><span class="sxs-lookup"><span data-stu-id="b9641-818">7.1       Inheritance Strategies</span></span>

<span data-ttu-id="b9641-819">使用實體框架時的另一個性能考慮是您使用的繼承策略。</span><span class="sxs-lookup"><span data-stu-id="b9641-819">Another performance consideration when using Entity Framework is the inheritance strategy you use.</span></span> <span data-ttu-id="b9641-820">實體框架支援 3 種基本類型的繼承與群組:</span><span class="sxs-lookup"><span data-stu-id="b9641-820">Entity Framework supports 3 basic types of inheritance and their combinations:</span></span>

-   <span data-ttu-id="b9641-821">每個層次結構的表 (TPH) - 其中每個繼承集映射到具有區分列的表,以指示在行中表示層次結構中的特定類型。</span><span class="sxs-lookup"><span data-stu-id="b9641-821">Table per Hierarchy (TPH) – where each inheritance set maps to a table with a discriminator column to indicate which particular type in the hierarchy is being represented in the row.</span></span>
-   <span data-ttu-id="b9641-822">每個類型 (TPT) 的表 - 其中每種類型在資料庫中都有自己的表;子表僅定義父表不包含的列。</span><span class="sxs-lookup"><span data-stu-id="b9641-822">Table per Type (TPT) – where each type has its own table in the database; the child tables only define the columns that the parent table doesn’t contain.</span></span>
-   <span data-ttu-id="b9641-823">每個類的表 (TPC) - 其中每種類型的在資料庫中都有自己的完整表;子表定義其所有欄位,包括父類型中定義的欄位。</span><span class="sxs-lookup"><span data-stu-id="b9641-823">Table per Class (TPC) – where each type has its own full table in the database; the child tables define all their fields, including those defined in parent types.</span></span>

<span data-ttu-id="b9641-824">如果模型使用 TPT 繼承,則生成的查詢將比其他繼承策略生成的查詢複雜,這可能導致存儲上的執行時間較長。</span><span class="sxs-lookup"><span data-stu-id="b9641-824">If your model uses TPT inheritance, the queries which are generated will be more complex than those that are generated with the other inheritance strategies, which may result on longer execution times on the store.</span></span><span data-ttu-id="b9641-825">通過 TPT 模型生成查詢並實現結果物件通常需要更長時間。</span><span class="sxs-lookup"><span data-stu-id="b9641-825">  It will generally take longer to generate queries over a TPT model, and to materialize the resulting objects.</span></span>

<span data-ttu-id="b9641-826">請參考「在實體框架中使用 TPT(按類型表)繼承時的效能注意事項「MSDN 」\<https://docs.microsoft.com/archive/blogs/adonet/performance-considerations-when-using-tpt-table-per-type-inheritance-in-the-entity-framework>文章: 。</span><span class="sxs-lookup"><span data-stu-id="b9641-826">See the "Performance Considerations when using TPT (Table per Type) Inheritance in the Entity Framework" MSDN blog post: \<https://docs.microsoft.com/archive/blogs/adonet/performance-considerations-when-using-tpt-table-per-type-inheritance-in-the-entity-framework>.</span></span>

#### <a name="711-avoiding-tpt-in-model-first-or-code-first-applications"></a><span data-ttu-id="b9641-827">7.1.1 避免在模型優先或代碼優先應用程式中使用 TPT</span><span class="sxs-lookup"><span data-stu-id="b9641-827">7.1.1       Avoiding TPT in Model First or Code First applications</span></span>

<span data-ttu-id="b9641-828">當您在具有 TPT 架構的現有資料庫上創建模型時,您沒有很多選項。</span><span class="sxs-lookup"><span data-stu-id="b9641-828">When you create a model over an existing database that has a TPT schema, you don't have many options.</span></span> <span data-ttu-id="b9641-829">但是,在使用 Model First 或代碼優先創建應用程式時,出於性能問題,應避免 TPT 繼承。</span><span class="sxs-lookup"><span data-stu-id="b9641-829">But when creating an application using Model First or Code First, you should avoid TPT inheritance for performance concerns.</span></span>

<span data-ttu-id="b9641-830">在實體設計器精靈中使用模型優先時,您將獲得模型中任何繼承的 TPT。</span><span class="sxs-lookup"><span data-stu-id="b9641-830">When you use Model First in the Entity Designer Wizard, you will get TPT for any inheritance in your model.</span></span> <span data-ttu-id="b9641-831">如果要使用 Model First 切換到 TPH 繼承策略,可以使用 Visual\<http://visualstudiogallery.msdn.microsoft.com/df3541c3-d833-4b65-b942-989e7ec74c87/>)Studio 庫 (中的實體設計器資料庫生成電源包)中的「實體設計器資料庫生成電源包」。</span><span class="sxs-lookup"><span data-stu-id="b9641-831">If you want to switch to a TPH inheritance strategy with Model First, you can use the "Entity Designer Database Generation Power Pack" available from the Visual Studio Gallery ( \<http://visualstudiogallery.msdn.microsoft.com/df3541c3-d833-4b65-b942-989e7ec74c87/>).</span></span>

<span data-ttu-id="b9641-832">當使用 Code First 配置具有繼承的模型映射時,EF 預設將使用 TPH,因此繼承層次結構中的所有實體都將映射到同一表。</span><span class="sxs-lookup"><span data-stu-id="b9641-832">When using Code First to configure the mapping of a model with inheritance, EF will use TPH by default, therefore all entities in the inheritance hierarchy will be mapped to the same table.</span></span> <span data-ttu-id="b9641-833">有關詳細資訊,請參閱 MSDN 雜誌[http://msdn.microsoft.com/magazine/hh126815.aspx](https://msdn.microsoft.com/magazine/hh126815.aspx)() 中「實體框架4.1中代碼優先」一文中的「使用流暢 API 映射」部分。</span><span class="sxs-lookup"><span data-stu-id="b9641-833">See the "Mapping with the Fluent API" section of the "Code First in Entity Framework4.1" article in MSDN Magazine ( [http://msdn.microsoft.com/magazine/hh126815.aspx](https://msdn.microsoft.com/magazine/hh126815.aspx)) for more details.</span></span>

### <a name="72-upgrading-from-ef4-to-improve-model-generation-time"></a><span data-ttu-id="b9641-834">7.2 從 EF4 升級以提高模型生成時間</span><span class="sxs-lookup"><span data-stu-id="b9641-834">7.2       Upgrading from EF4 to improve model generation time</span></span>

<span data-ttu-id="b9641-835">生成模型儲存層 (SSDL) 的演演演算法的 SQL Server 特定改進可在實體架構 5 和 6 中提供,並在安裝 Visual Studio 2010 SP1 時作為實體框架 4 的更新提供。</span><span class="sxs-lookup"><span data-stu-id="b9641-835">A SQL Server-specific improvement to the algorithm that generates the store-layer (SSDL) of the model is available in Entity Framework 5 and 6, and as an update to Entity Framework 4 when Visual Studio 2010 SP1 is installed.</span></span> <span data-ttu-id="b9641-836">以下測試結果演示了生成非常大的模型時的改進,在這種情況下,Navision 模型。</span><span class="sxs-lookup"><span data-stu-id="b9641-836">The following test results demonstrate the improvement when generating a very big model, in this case the Navision model.</span></span> <span data-ttu-id="b9641-837">有關附錄 C 的更多詳細資訊,請參閱附錄 C。</span><span class="sxs-lookup"><span data-stu-id="b9641-837">See Appendix C for more details about it.</span></span>

<span data-ttu-id="b9641-838">該模型包含 1005 個實體集和 4227 個關聯集。</span><span class="sxs-lookup"><span data-stu-id="b9641-838">The model contains 1005 entity sets and 4227 association sets.</span></span>

| <span data-ttu-id="b9641-839">組態</span><span class="sxs-lookup"><span data-stu-id="b9641-839">Configuration</span></span>                              | <span data-ttu-id="b9641-840">所消耗的時間的細目</span><span class="sxs-lookup"><span data-stu-id="b9641-840">Breakdown of time consumed</span></span>                                                                                                                                               |
|:-------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="b9641-841">視覺工作室 2010, 實體框架 4</span><span class="sxs-lookup"><span data-stu-id="b9641-841">Visual Studio 2010, Entity Framework 4</span></span>     | <span data-ttu-id="b9641-842">SSDL 產生: 2 小時 27 分鐘</span><span class="sxs-lookup"><span data-stu-id="b9641-842">SSDL Generation: 2 hr 27 min</span></span> <br/> <span data-ttu-id="b9641-843">對應:1 秒</span><span class="sxs-lookup"><span data-stu-id="b9641-843">Mapping Generation: 1 second</span></span> <br/> <span data-ttu-id="b9641-844">CSDL 產生:1 秒</span><span class="sxs-lookup"><span data-stu-id="b9641-844">CSDL Generation: 1 second</span></span> <br/> <span data-ttu-id="b9641-845">物件層產生:1 秒</span><span class="sxs-lookup"><span data-stu-id="b9641-845">ObjectLayer Generation: 1 second</span></span> <br/> <span data-ttu-id="b9641-846">檢視產生: 2 小時 14 分鐘</span><span class="sxs-lookup"><span data-stu-id="b9641-846">View Generation: 2 h 14 min</span></span> |
| <span data-ttu-id="b9641-847">視覺化工作室 2010 SP1, 實體架構 4</span><span class="sxs-lookup"><span data-stu-id="b9641-847">Visual Studio 2010 SP1, Entity Framework 4</span></span> | <span data-ttu-id="b9641-848">SSDL 產生:1 秒</span><span class="sxs-lookup"><span data-stu-id="b9641-848">SSDL Generation: 1 second</span></span> <br/> <span data-ttu-id="b9641-849">對應:1 秒</span><span class="sxs-lookup"><span data-stu-id="b9641-849">Mapping Generation: 1 second</span></span> <br/> <span data-ttu-id="b9641-850">CSDL 產生:1 秒</span><span class="sxs-lookup"><span data-stu-id="b9641-850">CSDL Generation: 1 second</span></span> <br/> <span data-ttu-id="b9641-851">物件層產生:1 秒</span><span class="sxs-lookup"><span data-stu-id="b9641-851">ObjectLayer Generation: 1 second</span></span> <br/> <span data-ttu-id="b9641-852">檢視產生: 1 小時 53 分鐘</span><span class="sxs-lookup"><span data-stu-id="b9641-852">View Generation: 1 hr 53 min</span></span>   |
| <span data-ttu-id="b9641-853">視覺工作室 2013, 實體框架 5</span><span class="sxs-lookup"><span data-stu-id="b9641-853">Visual Studio 2013, Entity Framework 5</span></span>     | <span data-ttu-id="b9641-854">SSDL 產生:1 秒</span><span class="sxs-lookup"><span data-stu-id="b9641-854">SSDL Generation: 1 second</span></span> <br/> <span data-ttu-id="b9641-855">對應:1 秒</span><span class="sxs-lookup"><span data-stu-id="b9641-855">Mapping Generation: 1 second</span></span> <br/> <span data-ttu-id="b9641-856">CSDL 產生:1 秒</span><span class="sxs-lookup"><span data-stu-id="b9641-856">CSDL Generation: 1 second</span></span> <br/> <span data-ttu-id="b9641-857">物件層產生:1 秒</span><span class="sxs-lookup"><span data-stu-id="b9641-857">ObjectLayer Generation: 1 second</span></span> <br/> <span data-ttu-id="b9641-858">檢視產生: 65 分鐘</span><span class="sxs-lookup"><span data-stu-id="b9641-858">View Generation: 65 minutes</span></span>    |
| <span data-ttu-id="b9641-859">視覺工作室 2013, 實體框架 6</span><span class="sxs-lookup"><span data-stu-id="b9641-859">Visual Studio 2013, Entity Framework 6</span></span>     | <span data-ttu-id="b9641-860">SSDL 產生:1 秒</span><span class="sxs-lookup"><span data-stu-id="b9641-860">SSDL Generation: 1 second</span></span> <br/> <span data-ttu-id="b9641-861">對應:1 秒</span><span class="sxs-lookup"><span data-stu-id="b9641-861">Mapping Generation: 1 second</span></span> <br/> <span data-ttu-id="b9641-862">CSDL 產生:1 秒</span><span class="sxs-lookup"><span data-stu-id="b9641-862">CSDL Generation: 1 second</span></span> <br/> <span data-ttu-id="b9641-863">物件層產生:1 秒</span><span class="sxs-lookup"><span data-stu-id="b9641-863">ObjectLayer Generation: 1 second</span></span> <br/> <span data-ttu-id="b9641-864">視圖生成:28 秒。</span><span class="sxs-lookup"><span data-stu-id="b9641-864">View Generation: 28 seconds.</span></span>   |


<span data-ttu-id="b9641-865">值得注意的是,在生成 SSDL 時,負載幾乎完全花費在 SQL Server 上,而客戶端開發電腦正在等待從伺服器返回的結果。</span><span class="sxs-lookup"><span data-stu-id="b9641-865">It's worth noting that when generating the SSDL, the load is almost entirely spent on the SQL Server, while the client development machine is waiting idle for results to come back from the server.</span></span> <span data-ttu-id="b9641-866">DBA 應特別讚賞這一改進。</span><span class="sxs-lookup"><span data-stu-id="b9641-866">DBAs should particularly appreciate this improvement.</span></span> <span data-ttu-id="b9641-867">還值得注意的是,模型生成的全部成本基本上都發生在視圖生成中。</span><span class="sxs-lookup"><span data-stu-id="b9641-867">It's also worth noting that essentially the entire cost of model generation takes place in View Generation now.</span></span>

### <a name="73-splitting-large-models-with-database-first-and-model-first"></a><span data-ttu-id="b9641-868">7.3 使用資料庫優先和模型第一拆分大型模型</span><span class="sxs-lookup"><span data-stu-id="b9641-868">7.3       Splitting Large Models with Database First and Model First</span></span>

<span data-ttu-id="b9641-869">隨著模型尺寸的增加,設計器表面變得雜亂無章且難以使用。</span><span class="sxs-lookup"><span data-stu-id="b9641-869">As model size increases, the designer surface becomes cluttered and difficult to use.</span></span> <span data-ttu-id="b9641-870">我們通常認為具有 300 多個實體的模型太大,無法有效地使用設計器。</span><span class="sxs-lookup"><span data-stu-id="b9641-870">We typically consider a model with more than 300 entities to be too large to effectively use the designer.</span></span> <span data-ttu-id="b9641-871">以下部落格文章描述了分割大型模型的幾個選項\<https://docs.microsoft.com/archive/blogs/adonet/working-with-large-models-in-entity-framework-part-2>: 。</span><span class="sxs-lookup"><span data-stu-id="b9641-871">The following blog post describes several options for splitting large models: \<https://docs.microsoft.com/archive/blogs/adonet/working-with-large-models-in-entity-framework-part-2>.</span></span>

<span data-ttu-id="b9641-872">該帖子是為實體框架的第一個版本編寫的,但步驟仍然適用。</span><span class="sxs-lookup"><span data-stu-id="b9641-872">The post was written for the first version of Entity Framework, but the steps still apply.</span></span>

### <a name="74-performance-considerations-with-the-entity-data-source-control"></a><span data-ttu-id="b9641-873">7.4 實體資料來源控制的效能注意事項</span><span class="sxs-lookup"><span data-stu-id="b9641-873">7.4       Performance considerations with the Entity Data Source Control</span></span>

<span data-ttu-id="b9641-874">我們已經看到多線程性能和壓力測試中的情況,其中使用 EntityDataSource 控件的 Web 應用程式的性能顯著惡化。</span><span class="sxs-lookup"><span data-stu-id="b9641-874">We've seen cases in multi-threaded performance and stress tests where the performance of a web application using the EntityDataSource Control deteriorates significantly.</span></span> <span data-ttu-id="b9641-875">根本原因是 EntityDataSource 反復調用 Web 應用程式引用的程式集上的元數據工作區.Load From Assembly,以發現要用作實體的類型。</span><span class="sxs-lookup"><span data-stu-id="b9641-875">The underlying cause is that the EntityDataSource repeatedly calls MetadataWorkspace.LoadFromAssembly on the assemblies referenced by the Web application to discover the types to be used as entities.</span></span>

<span data-ttu-id="b9641-876">解決方案是將實體數據來源的上下文類型名稱設置為派生物件上下文類的類型名稱。</span><span class="sxs-lookup"><span data-stu-id="b9641-876">The solution is to set the ContextTypeName of the EntityDataSource to the type name of your derived ObjectContext class.</span></span> <span data-ttu-id="b9641-877">這將關閉掃描所有引用的程式集的實體類型的機制。</span><span class="sxs-lookup"><span data-stu-id="b9641-877">This turns off the mechanism that scans all referenced assemblies for entity types.</span></span>

<span data-ttu-id="b9641-878">設置 ContextTypeName 欄位還可以防止功能問題,即 .NET 4.0 中的實體資料來源在無法透過反射從程式集載入類型時引發反射類型載入異常。</span><span class="sxs-lookup"><span data-stu-id="b9641-878">Setting the ContextTypeName field also prevents a functional problem where the EntityDataSource in .NET 4.0 throws a ReflectionTypeLoadException when it can't load a type from an assembly via reflection.</span></span> <span data-ttu-id="b9641-879">此問題已在 .NET 4.5 中修復。</span><span class="sxs-lookup"><span data-stu-id="b9641-879">This issue has been fixed in .NET 4.5.</span></span>

### <a name="75-poco-entities-and-change-tracking-proxies"></a><span data-ttu-id="b9641-880">7.5 POCO 實體和變更追蹤代理</span><span class="sxs-lookup"><span data-stu-id="b9641-880">7.5       POCO entities and change tracking proxies</span></span>

<span data-ttu-id="b9641-881">實體框架使您能夠將自定義數據類與數據模型一起使用,而無需對數據類本身進行任何修改。</span><span class="sxs-lookup"><span data-stu-id="b9641-881">Entity Framework enables you to use custom data classes together with your data model without making any modifications to the data classes themselves.</span></span> <span data-ttu-id="b9641-882">這表示您可以使用「單純」(plain-old) CLR 物件 (POCO)，例如現有的網域物件，加上您的資料模型。</span><span class="sxs-lookup"><span data-stu-id="b9641-882">This means that you can use "plain-old" CLR objects (POCO), such as existing domain objects, with your data model.</span></span> <span data-ttu-id="b9641-883">這些 POCO 資料類(也稱為持久性無知物件)映射到數據模型中定義的實體,支援大多數相同的查詢,插入、更新和刪除實體數據模型工具生成的實體類型的行為。</span><span class="sxs-lookup"><span data-stu-id="b9641-883">These POCO data classes (also known as persistence-ignorant objects), which are mapped to entities that are defined in a data model, support most of the same query, insert, update, and delete behaviors as entity types that are generated by the Entity Data Model tools.</span></span>

<span data-ttu-id="b9641-884">實體框架還可以創建從 POCO 類型派生的代理類,當您希望啟用諸如 POCO 實體上的延遲載入和自動更改追蹤等功能時,將使用這些類。</span><span class="sxs-lookup"><span data-stu-id="b9641-884">Entity Framework can also create proxy classes derived from your POCO types, which are used when you want to enable features such as lazy loading and automatic change tracking on POCO entities.</span></span> <span data-ttu-id="b9641-885">您的 POCO 類別必須滿足某些要求,才能允許實體架構使用代理,如下所[http://msdn.microsoft.com/library/dd468057.aspx](https://msdn.microsoft.com/library/dd468057.aspx)述: 。</span><span class="sxs-lookup"><span data-stu-id="b9641-885">Your POCO classes must meet certain requirements to allow Entity Framework to use proxies, as described here: [http://msdn.microsoft.com/library/dd468057.aspx](https://msdn.microsoft.com/library/dd468057.aspx).</span></span>

<span data-ttu-id="b9641-886">每次實體的任何屬性發生更改時,機會跟蹤代理都會通知物件狀態管理器,因此實體框架會一直瞭解實體的實際狀態。</span><span class="sxs-lookup"><span data-stu-id="b9641-886">Chance tracking proxies will notify the object state manager each time any of the properties of your entities has its value changed, so Entity Framework knows the actual state of your entities all the time.</span></span> <span data-ttu-id="b9641-887">這是通過將通知事件添加到屬性的 setter 方法的正文,以及讓物件狀態管理器處理此類事件來實現的。</span><span class="sxs-lookup"><span data-stu-id="b9641-887">This is done by adding notification events to the body of the setter methods of your properties, and having the object state manager processing such events.</span></span> <span data-ttu-id="b9641-888">請注意,由於實體框架創建了一組附加的事件,創建代理實體通常比創建非代理 POCO 實體的成本更高。</span><span class="sxs-lookup"><span data-stu-id="b9641-888">Note that creating a proxy entity will typically be more expensive than creating a non-proxy POCO entity due to the added set of events created by Entity Framework.</span></span>

<span data-ttu-id="b9641-889">當 POCO 實體沒有更改追蹤代理時,可以通過將實體的內容與以前保存狀態的副本進行比較來找到更改。</span><span class="sxs-lookup"><span data-stu-id="b9641-889">When a POCO entity does not have a change tracking proxy, changes are found by comparing the contents of your entities against a copy of a previous saved state.</span></span> <span data-ttu-id="b9641-890">當您的上下文中有許多實體時,或者當實體具有非常大的屬性時,即使自上次比較以來,這些屬性均未更改,這種深層比較將成為一個漫長的過程。</span><span class="sxs-lookup"><span data-stu-id="b9641-890">This deep comparison will become a lengthy process when you have many entities in your context, or when your entities have a very large amount of properties, even if none of them changed since the last comparison took place.</span></span>

<span data-ttu-id="b9641-891">總之:創建更改跟蹤代理時,您將支付性能影響,但當實體具有許多屬性或模型中有許多實體時,更改跟蹤將説明您加快更改檢測過程。</span><span class="sxs-lookup"><span data-stu-id="b9641-891">In summary: you’ll pay a performance hit when creating the change tracking proxy, but change tracking will help you speed up the change detection process when your entities have many properties or when you have many entities in your model.</span></span> <span data-ttu-id="b9641-892">對於具有少量屬性的實體,其中實體數量不會增長太多,因此具有更改跟蹤代理可能沒有多大好處。</span><span class="sxs-lookup"><span data-stu-id="b9641-892">For entities with a small number of properties where the amount of entities doesn’t grow too much, having change tracking proxies may not be of much benefit.</span></span>

## <a name="8-loading-related-entities"></a><span data-ttu-id="b9641-893">8 載入相關實體</span><span class="sxs-lookup"><span data-stu-id="b9641-893">8 Loading Related Entities</span></span>

### <a name="81-lazy-loading-vs-eager-loading"></a><span data-ttu-id="b9641-894">8.1 延遲載入與熱負荷</span><span class="sxs-lookup"><span data-stu-id="b9641-894">8.1 Lazy Loading vs. Eager Loading</span></span>

<span data-ttu-id="b9641-895">實體框架提供了幾種載入與目標實體相關的實體的不同方法。</span><span class="sxs-lookup"><span data-stu-id="b9641-895">Entity Framework offers several different ways to load the entities that are related to your target entity.</span></span> <span data-ttu-id="b9641-896">例如,當您查詢產品時,相關訂單將載入到物件狀態管理器中的方式不同。</span><span class="sxs-lookup"><span data-stu-id="b9641-896">For example, when you query for Products, there are different ways that the related Orders will be loaded into the Object State Manager.</span></span> <span data-ttu-id="b9641-897">從性能角度來看,載入相關實體時要考慮的最大問題是是使用延遲載入還是"渴望載入」。</span><span class="sxs-lookup"><span data-stu-id="b9641-897">From a performance standpoint, the biggest question to consider when loading related entities will be whether to use Lazy Loading or Eager Loading.</span></span>

<span data-ttu-id="b9641-898">使用「熱載入」時,相關實體將隨目標實體集一起載入。</span><span class="sxs-lookup"><span data-stu-id="b9641-898">When using Eager Loading, the related entities are loaded along with your target entity set.</span></span> <span data-ttu-id="b9641-899">在查詢中使用"包含"語句來指示要引入哪些相關實體。</span><span class="sxs-lookup"><span data-stu-id="b9641-899">You use an Include statement in your query to indicate which related entities you want to bring in.</span></span>

<span data-ttu-id="b9641-900">使用延遲載入時,初始查詢僅引入目標實體集。</span><span class="sxs-lookup"><span data-stu-id="b9641-900">When using Lazy Loading, your initial query only brings in the target entity set.</span></span> <span data-ttu-id="b9641-901">但是,每當訪問導航屬性時,都會針對存儲發出另一個查詢以載入相關實體。</span><span class="sxs-lookup"><span data-stu-id="b9641-901">But whenever you access a navigation property, another query is issued against the store to load the related entity.</span></span>

<span data-ttu-id="b9641-902">載入實體後,實體的任何進一步查詢都將直接從物件狀態管理器載入它,無論您是使用延遲載入還是熱要載入。</span><span class="sxs-lookup"><span data-stu-id="b9641-902">Once an entity has been loaded, any further queries for the entity will load it directly from the Object State Manager, whether you are using lazy loading or eager loading.</span></span>

### <a name="82-how-to-choose-between-lazy-loading-and-eager-loading"></a><span data-ttu-id="b9641-903">8.2 如何在延遲載入和渴望載入之間進行選擇</span><span class="sxs-lookup"><span data-stu-id="b9641-903">8.2 How to choose between Lazy Loading and Eager Loading</span></span>

<span data-ttu-id="b9641-904">重要的是,您瞭解延遲載入和熱載入之間的區別,以便您可以為您的應用程式做出正確的選擇。</span><span class="sxs-lookup"><span data-stu-id="b9641-904">The important thing is that you understand the difference between Lazy Loading and Eager Loading so that you can make the correct choice for your application.</span></span> <span data-ttu-id="b9641-905">這將有助於評估針對資料庫的多個請求與可能包含大型負載的單個請求之間的權衡。</span><span class="sxs-lookup"><span data-stu-id="b9641-905">This will help you evaluate the tradeoff between multiple requests against the database versus a single request that may contain a large payload.</span></span> <span data-ttu-id="b9641-906">在應用程式的某些部分使用熱載入和其他部分的延遲載入可能是合適的。</span><span class="sxs-lookup"><span data-stu-id="b9641-906">It may be appropriate to use eager loading in some parts of your application and lazy loading in other parts.</span></span>

<span data-ttu-id="b9641-907">作為引擎蓋下所發生的事情的一個示例,假設您要查詢居住在英國的客戶及其訂單數量。</span><span class="sxs-lookup"><span data-stu-id="b9641-907">As an example of what's happening under the hood, suppose you want to query for the customers who live in the UK and their order count.</span></span>

<span data-ttu-id="b9641-908">**使用熱負荷**</span><span class="sxs-lookup"><span data-stu-id="b9641-908">**Using Eager Loading**</span></span>

``` csharp
using (NorthwindEntities context = new NorthwindEntities())
{
    var ukCustomers = context.Customers.Include(c => c.Orders).Where(c => c.Address.Country == "UK");
    var chosenCustomer = AskUserToPickCustomer(ukCustomers);
    Console.WriteLine("Customer Id: {0} has {1} orders", customer.CustomerID, customer.Orders.Count);
}
```

<span data-ttu-id="b9641-909">**使用延遲載入**</span><span class="sxs-lookup"><span data-stu-id="b9641-909">**Using Lazy Loading**</span></span>

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

<span data-ttu-id="b9641-910">使用熱要載入時,您將發出一個查詢,返回所有客戶和所有訂單。</span><span class="sxs-lookup"><span data-stu-id="b9641-910">When using eager loading, you'll issue a single query that returns all customers and all orders.</span></span> <span data-ttu-id="b9641-911">儲存命令如下所示:</span><span class="sxs-lookup"><span data-stu-id="b9641-911">The store command looks like:</span></span>

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

<span data-ttu-id="b9641-912">使用延遲載入時,最初將發出以下查詢:</span><span class="sxs-lookup"><span data-stu-id="b9641-912">When using lazy loading, you'll issue the following query initially:</span></span>

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

<span data-ttu-id="b9641-913">每次訪問客戶的「訂單」導航屬性時,都會針對商店發出另一個查詢,如下所示:</span><span class="sxs-lookup"><span data-stu-id="b9641-913">And each time you access the Orders navigation property of a customer another query like the following is issued against the store:</span></span>

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

<span data-ttu-id="b9641-914">有關詳細資訊,請參閱[載入相關物件](https://msdn.microsoft.com/library/bb896272.aspx)。</span><span class="sxs-lookup"><span data-stu-id="b9641-914">For more information, see the [Loading Related Objects](https://msdn.microsoft.com/library/bb896272.aspx).</span></span>

#### <a name="821-lazy-loading-versus-eager-loading-cheat-sheet"></a><span data-ttu-id="b9641-915">8.2.1 延遲載入與渴望載入備忘單</span><span class="sxs-lookup"><span data-stu-id="b9641-915">8.2.1 Lazy Loading versus Eager Loading cheat sheet</span></span>

<span data-ttu-id="b9641-916">選擇熱切裝載和延遲裝載,沒有一刀切的東西。</span><span class="sxs-lookup"><span data-stu-id="b9641-916">There’s no such thing as a one-size-fits-all to choosing eager loading versus lazy loading.</span></span> <span data-ttu-id="b9641-917">首先嘗試瞭解兩種策略之間的差異,以便您可以做出明智的決策;此外,請考慮您的代碼是否適合以下任何方案:</span><span class="sxs-lookup"><span data-stu-id="b9641-917">Try first to understand the differences between both strategies so you can do a well informed decision; also, consider if your code fits to any of the following scenarios:</span></span>

| <span data-ttu-id="b9641-918">狀況</span><span class="sxs-lookup"><span data-stu-id="b9641-918">Scenario</span></span>                                                                    | <span data-ttu-id="b9641-919">我們的建議</span><span class="sxs-lookup"><span data-stu-id="b9641-919">Our Suggestion</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
|:----------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="b9641-920">是否需要從提取的實體訪問許多導航屬性?</span><span class="sxs-lookup"><span data-stu-id="b9641-920">Do you need to access many navigation properties from the fetched entities?</span></span> | <span data-ttu-id="b9641-921">**否**- 兩個選項都可能都能做到。</span><span class="sxs-lookup"><span data-stu-id="b9641-921">**No** - Both options will probably do.</span></span> <span data-ttu-id="b9641-922">但是,如果查詢帶來的有效負載不是太大,則使用 Eager 載入可能會獲得性能優勢,因為它需要較少的網路往返行程來實現物件。</span><span class="sxs-lookup"><span data-stu-id="b9641-922">However, if the payload your query is bringing is not too big, you may experience performance benefits by using Eager loading as it’ll require less network round trips to materialize your objects.</span></span> <br/> <br/> <span data-ttu-id="b9641-923">**是**- 如果需要從實體訪問許多導航屬性,則可以通過在查詢中使用多個包含語句來使用"渴望載入"來執行此操作。</span><span class="sxs-lookup"><span data-stu-id="b9641-923">**Yes** -  If you need to access many navigation properties from the entities, you’d do that by using multiple include statements in your query with Eager loading.</span></span> <span data-ttu-id="b9641-924">包含的實體越多,查詢返回的有效負載就越大。</span><span class="sxs-lookup"><span data-stu-id="b9641-924">The more entities you include, the bigger the payload your query will return.</span></span> <span data-ttu-id="b9641-925">將三個或多個實體包含在查詢中後,請考慮切換到延遲載入。</span><span class="sxs-lookup"><span data-stu-id="b9641-925">Once you include three or more entities into your query, consider switching to Lazy loading.</span></span> |
| <span data-ttu-id="b9641-926">您確切知道運行時需要哪些數據嗎?</span><span class="sxs-lookup"><span data-stu-id="b9641-926">Do you know exactly what data will be needed at run time?</span></span>                   | <span data-ttu-id="b9641-927">**否**- 延遲載入將更好地為您。</span><span class="sxs-lookup"><span data-stu-id="b9641-927">**No** - Lazy loading will be better for you.</span></span> <span data-ttu-id="b9641-928">否則,您最終可能會查詢不需要的數據。</span><span class="sxs-lookup"><span data-stu-id="b9641-928">Otherwise, you may end up querying for data that you will not need.</span></span> <br/> <br/> <span data-ttu-id="b9641-929">**是的**- 渴望載入可能是您最好的選擇;這將有助於更快地載入整個集。</span><span class="sxs-lookup"><span data-stu-id="b9641-929">**Yes** - Eager loading is probably your best bet; it will help loading entire sets faster.</span></span> <span data-ttu-id="b9641-930">如果查詢需要獲取大量數據,並且速度太慢,請嘗試"延遲載入"</span><span class="sxs-lookup"><span data-stu-id="b9641-930">If your query requires fetching a very large amount of data, and this becomes too slow, then try Lazy loading instead.</span></span>                                                                                                                                                                                                                                                       |
| <span data-ttu-id="b9641-931">您的代碼執行是否遠離資料庫?</span><span class="sxs-lookup"><span data-stu-id="b9641-931">Is your code executing far from your database?</span></span> <span data-ttu-id="b9641-932">(增加網路延遲)</span><span class="sxs-lookup"><span data-stu-id="b9641-932">(increased network latency)</span></span>  | <span data-ttu-id="b9641-933">**否**- 當網路延遲不是問題時,使用延遲載入可能會簡化代碼。</span><span class="sxs-lookup"><span data-stu-id="b9641-933">**No** - When the network latency is not an issue, using Lazy loading may simplify your code.</span></span> <span data-ttu-id="b9641-934">請記住,應用程式的拓撲可能會更改,因此不要認為資料庫鄰近性是理所當然的。</span><span class="sxs-lookup"><span data-stu-id="b9641-934">Remember that the topology of your application may change, so don’t take database proximity for granted.</span></span> <br/> <br/> <span data-ttu-id="b9641-935">**是**- 當網路出現問題時,只有您才能決定哪種方案更適合您的方案。</span><span class="sxs-lookup"><span data-stu-id="b9641-935">**Yes** - When the network is a problem, only you can decide what fits better for your scenario.</span></span> <span data-ttu-id="b9641-936">通常,熱負荷會更好,因為它需要更少的往返行程。</span><span class="sxs-lookup"><span data-stu-id="b9641-936">Typically Eager loading will be better because it requires fewer round trips.</span></span>                                                                                                                                                                                                      |


#### <a name="822-performance-concerns-with-multiple-includes"></a><span data-ttu-id="b9641-937">8.2.2 具有多個包括的性能問題</span><span class="sxs-lookup"><span data-stu-id="b9641-937">8.2.2       Performance concerns with multiple Includes</span></span>

<span data-ttu-id="b9641-938">當我們聽到涉及伺服器回應時間問題的性能問題時,問題的根源是經常使用多個 Include 語句進行查詢。</span><span class="sxs-lookup"><span data-stu-id="b9641-938">When we hear performance questions that involve server response time problems, the source of the issue is frequently queries with multiple Include statements.</span></span> <span data-ttu-id="b9641-939">雖然在查詢中包含相關實體很強大,但瞭解封面下發生的情況非常重要。</span><span class="sxs-lookup"><span data-stu-id="b9641-939">While including related entities in a query is powerful, it's important to understand what's happening under the covers.</span></span>

<span data-ttu-id="b9641-940">包含多個 Include 語句的查詢需要相當長的時間才能通過內部計劃編譯器來生成儲存命令。</span><span class="sxs-lookup"><span data-stu-id="b9641-940">It takes a relatively long time for a query with multiple Include statements in it to go through our internal plan compiler to produce the store command.</span></span> <span data-ttu-id="b9641-941">這一大部分時間都花在嘗試優化生成的查詢上。</span><span class="sxs-lookup"><span data-stu-id="b9641-941">The majority of this time is spent trying to optimize the resulting query.</span></span> <span data-ttu-id="b9641-942">生成的存儲命令將包含每個"包含"的外部聯接或聯合,具體取決於您的映射。</span><span class="sxs-lookup"><span data-stu-id="b9641-942">The generated store command will contain an Outer Join or Union for each Include, depending on your mapping.</span></span> <span data-ttu-id="b9641-943">像這樣的查詢將在單個負載中從資料庫引入大型連接圖形,從而消除任何頻寬問題,尤其是在有效負載中存在大量冗餘時(例如,當多個級別的 Include 用於在一對多方向上遍歷關聯時)。</span><span class="sxs-lookup"><span data-stu-id="b9641-943">Queries like this will bring in large connected graphs from your database in a single payload, which will acerbate any bandwidth issues, especially when there is a lot of redundancy in the payload (for example, when multiple levels of Include are used to traverse associations in the one-to-many direction).</span></span>

<span data-ttu-id="b9641-944">通過使用 ToTraceString 並在 SQL 伺服器管理工作室中執行存儲命令以查看有效負載大小,可以檢查查詢返回過大的有效負載的情況。</span><span class="sxs-lookup"><span data-stu-id="b9641-944">You can check for cases where your queries are returning excessively large payloads by accessing the underlying TSQL for the query by using ToTraceString and executing the store command in SQL Server Management Studio to see the payload size.</span></span> <span data-ttu-id="b9641-945">在這種情況下,您可以嘗試減少查詢中的包含語句數,以便僅引入所需的數據。</span><span class="sxs-lookup"><span data-stu-id="b9641-945">In such cases you can try to reduce the number of Include statements in your query to just bring in the data you need.</span></span> <span data-ttu-id="b9641-946">或者,您可能能夠將查詢分解為較小的子查詢序列,例如:</span><span class="sxs-lookup"><span data-stu-id="b9641-946">Or you may be able to break your query into a smaller sequence of subqueries, for example:</span></span>

<span data-ttu-id="b9641-947">**在斷開查詢之前:**</span><span class="sxs-lookup"><span data-stu-id="b9641-947">**Before breaking the query:**</span></span>

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

<span data-ttu-id="b9641-948">**中斷查詢後:**</span><span class="sxs-lookup"><span data-stu-id="b9641-948">**After breaking the query:**</span></span>

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

<span data-ttu-id="b9641-949">這僅適用於追蹤的查詢,因為我們正在利用上下文自動執行標識解析和關聯修復的能力。</span><span class="sxs-lookup"><span data-stu-id="b9641-949">This will work only on tracked queries, as we are making use of the ability the context has to perform identity resolution and association fixup automatically.</span></span>

<span data-ttu-id="b9641-950">與延遲載入一樣,權衡將是對較小負載的更多查詢。</span><span class="sxs-lookup"><span data-stu-id="b9641-950">As with lazy loading, the tradeoff will be more queries for smaller payloads.</span></span> <span data-ttu-id="b9641-951">您還可以使用單個屬性的預測來僅顯式選擇每個實體所需的數據,但在這種情況下,您將不會載入實體,並且不支援更新。</span><span class="sxs-lookup"><span data-stu-id="b9641-951">You can also use projections of individual properties to explicitly select only the data you need from each entity, but you will not be loading entities in this case, and updates will not be supported.</span></span>

#### <a name="823-workaround-to-get-lazy-loading-of-properties"></a><span data-ttu-id="b9641-952">8.2.3 取得延遲載入屬性的解決方法</span><span class="sxs-lookup"><span data-stu-id="b9641-952">8.2.3 Workaround to get lazy loading of properties</span></span>

<span data-ttu-id="b9641-953">實體框架當前不支援延遲載入標量或複雜屬性。</span><span class="sxs-lookup"><span data-stu-id="b9641-953">Entity Framework currently doesn’t support lazy loading of scalar or complex properties.</span></span> <span data-ttu-id="b9641-954">但是,如果表包含大型物件(如 BLOB),則可以使用表拆分將大型屬性分隔為單獨的實體。</span><span class="sxs-lookup"><span data-stu-id="b9641-954">However, in cases where you have a table that includes a large object such as a BLOB, you can use table splitting to separate the large properties into a separate entity.</span></span> <span data-ttu-id="b9641-955">例如,假設您有一個包含 varbinary 照片列的產品表。</span><span class="sxs-lookup"><span data-stu-id="b9641-955">For example, suppose you have a Product table that includes a varbinary photo column.</span></span> <span data-ttu-id="b9641-956">如果查詢中頻繁不需要訪問此屬性,則可以使用表拆分僅引入通常需要的實體部分。</span><span class="sxs-lookup"><span data-stu-id="b9641-956">If you don't frequently need to access this property in your queries, you can use table splitting to bring in only the parts of the entity that you normally need.</span></span> <span data-ttu-id="b9641-957">僅當您明確需要產品照片時,才會載入表示產品照片的實體。</span><span class="sxs-lookup"><span data-stu-id="b9641-957">The entity representing the product photo will only be loaded when you explicitly need it.</span></span>

<span data-ttu-id="b9641-958">吉爾·芬克的「實體框架中的表拆分」博客文章是演示如何啟用表拆分的一個很好的資源\<http://blogs.microsoft.co.il/blogs/gilf/archive/2009/10/13/table-splitting-in-entity-framework.aspx>: .</span><span class="sxs-lookup"><span data-stu-id="b9641-958">A good resource that shows how to enable table splitting is Gil Fink's "Table Splitting in Entity Framework" blog post: \<http://blogs.microsoft.co.il/blogs/gilf/archive/2009/10/13/table-splitting-in-entity-framework.aspx>.</span></span>

## <a name="9-other-considerations"></a><span data-ttu-id="b9641-959">9 其他注意事項</span><span class="sxs-lookup"><span data-stu-id="b9641-959">9 Other considerations</span></span>

### <a name="91-server-garbage-collection"></a><span data-ttu-id="b9641-960">9.1 伺服器垃圾回收</span><span class="sxs-lookup"><span data-stu-id="b9641-960">9.1      Server Garbage Collection</span></span>

<span data-ttu-id="b9641-961">某些使用者可能會遇到資源爭用,從而限制在垃圾回收器未正確配置時期望的並行性。</span><span class="sxs-lookup"><span data-stu-id="b9641-961">Some users might experience resource contention that limits the parallelism they are expecting when the Garbage Collector is not properly configured.</span></span> <span data-ttu-id="b9641-962">每當在多線程方案中使用 EF 時,或任何類似於伺服器端系統的應用程式中,請確保啟用伺服器垃圾回收。</span><span class="sxs-lookup"><span data-stu-id="b9641-962">Whenever EF is used in a multithreaded scenario, or in any application that resembles a server-side system, make sure to enable Server Garbage Collection.</span></span> <span data-ttu-id="b9641-963">這是透過應用程式設定檔中的簡單設定完成的:</span><span class="sxs-lookup"><span data-stu-id="b9641-963">This is done via a simple setting in your application config file:</span></span>

``` xml
<?xmlversion="1.0" encoding="utf-8" ?>
<configuration>
        <runtime>
               <gcServer enabled="true" />
        </runtime>
</configuration>
```

<span data-ttu-id="b9641-964">這將減少線程爭用,並在 CPU 飽和方案中將輸送量提高高達 30%。</span><span class="sxs-lookup"><span data-stu-id="b9641-964">This should decrease your thread contention and increase your throughput by up to 30% in CPU saturated scenarios.</span></span> <span data-ttu-id="b9641-965">通常,您應該始終使用經典垃圾回收(更好地針對 UI 和用戶端方案)以及伺服器垃圾回收測試應用程式的表現。</span><span class="sxs-lookup"><span data-stu-id="b9641-965">In general terms, you should always test how your application behaves using the classic Garbage Collection (which is better tuned for UI and client side scenarios) as well as the Server Garbage Collection.</span></span>

### <a name="92-autodetectchanges"></a><span data-ttu-id="b9641-966">9.2 自動偵測變更</span><span class="sxs-lookup"><span data-stu-id="b9641-966">9.2      AutoDetectChanges</span></span>

<span data-ttu-id="b9641-967">如前所述,當物件緩存具有多個實體時,實體框架可能會顯示性能問題。</span><span class="sxs-lookup"><span data-stu-id="b9641-967">As mentioned earlier, Entity Framework might show performance issues when the object cache has many entities.</span></span> <span data-ttu-id="b9641-968">某些操作(如添加、刪除、尋找、輸入和保存更改)會觸發對檢測更改的調用,這些調用可能會根據物件緩存變得多大而消耗大量 CPU。</span><span class="sxs-lookup"><span data-stu-id="b9641-968">Certain operations, such as Add, Remove, Find, Entry and SaveChanges, trigger calls to DetectChanges which might consume a large amount of CPU based on how large the object cache has become.</span></span> <span data-ttu-id="b9641-969">原因是物件緩存和物件狀態管理器嘗試在對上下文執行的每個操作上保持盡可能同步,以便在各種方案下保證生成的數據正確。</span><span class="sxs-lookup"><span data-stu-id="b9641-969">The reason for this is that the object cache and the object state manager try to stay as synchronized as possible on each operation performed to a context so that the produced data is guaranteed to be correct under a wide array of scenarios.</span></span>

<span data-ttu-id="b9641-970">通常,最好在應用程式的整個生命週期內啟用實體框架的自動更改檢測。</span><span class="sxs-lookup"><span data-stu-id="b9641-970">It is generally a good practice to leave Entity Framework’s automatic change detection enabled for the entire life of your application.</span></span> <span data-ttu-id="b9641-971">如果您的方案受到 CPU 使用率高的負面影響,並且您的設定檔指示罪魁禍首是調用檢測更改,請考慮暫時關閉程式碼敏感部分中的自動檢測更改:</span><span class="sxs-lookup"><span data-stu-id="b9641-971">If your scenario is being negatively affected by high CPU usage and your profiles indicate that the culprit is the call to DetectChanges, consider temporarily turning off AutoDetectChanges in the sensitive portion of your code:</span></span>

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

<span data-ttu-id="b9641-972">在關閉自動檢測更改之前,最好瞭解這可能會導致實體框架失去跟蹤有關實體上發生的更改的某些資訊的能力。</span><span class="sxs-lookup"><span data-stu-id="b9641-972">Before turning off AutoDetectChanges, it’s good to understand that this might cause Entity Framework to lose its ability to track certain information about the changes that are taking place on the entities.</span></span> <span data-ttu-id="b9641-973">如果處理不正確,則可能會導致應用程式上的數據不一致。</span><span class="sxs-lookup"><span data-stu-id="b9641-973">If handled incorrectly, this might cause data inconsistency on your application.</span></span> <span data-ttu-id="b9641-974">關於關閉自動偵測變更的詳細資訊,請\<http://blog.oneunicorn.com/2012/03/12/secrets-of-detectchanges-part-3-switching-off-automatic-detectchanges/>閱讀 。</span><span class="sxs-lookup"><span data-stu-id="b9641-974">For more information on turning off AutoDetectChanges, read \<http://blog.oneunicorn.com/2012/03/12/secrets-of-detectchanges-part-3-switching-off-automatic-detectchanges/>.</span></span>

### <a name="93-context-per-request"></a><span data-ttu-id="b9641-975">9.3 每個要求的上下文</span><span class="sxs-lookup"><span data-stu-id="b9641-975">9.3      Context per request</span></span>

<span data-ttu-id="b9641-976">實體框架的上下文旨在用作短期實例,以便提供最佳的性能體驗。</span><span class="sxs-lookup"><span data-stu-id="b9641-976">Entity Framework’s contexts are meant to be used as short-lived instances in order to provide the most optimal performance experience.</span></span> <span data-ttu-id="b9641-977">上下文預期是短暫的和丟棄的,因此已經實現為非常輕量級,並盡可能重用元數據。</span><span class="sxs-lookup"><span data-stu-id="b9641-977">Contexts are expected to be short lived and discarded, and as such have been implemented to be very lightweight and reutilize metadata whenever possible.</span></span> <span data-ttu-id="b9641-978">在 Web 方案中,請務必牢記這一點,並且不應具有超過單個請求持續時間的上下文。</span><span class="sxs-lookup"><span data-stu-id="b9641-978">In web scenarios it’s important to keep this in mind and not have a context for more than the duration of a single request.</span></span> <span data-ttu-id="b9641-979">同樣,在非 Web 方案中,應根據您對實體框架中不同緩存級別的理解而丟棄上下文。</span><span class="sxs-lookup"><span data-stu-id="b9641-979">Similarly, in non-web scenarios, context should be discarded based on your understanding of the different levels of caching in the Entity Framework.</span></span> <span data-ttu-id="b9641-980">一般來說,在應用程式的整個生命週期中,應避免使用上下文實例,以及每個線程和靜態上下文的上下文。</span><span class="sxs-lookup"><span data-stu-id="b9641-980">Generally speaking, one should avoid having a context instance throughout the life of the application, as well as contexts per thread and static contexts.</span></span>

### <a name="94-database-null-semantics"></a><span data-ttu-id="b9641-981">9.4 資料庫無效文意</span><span class="sxs-lookup"><span data-stu-id="b9641-981">9.4      Database null semantics</span></span>

<span data-ttu-id="b9641-982">默認情況下,實體框架將生成具有\#C 空比較語義的 SQL 代碼。</span><span class="sxs-lookup"><span data-stu-id="b9641-982">Entity Framework by default will generate SQL code that has C\# null comparison semantics.</span></span> <span data-ttu-id="b9641-983">請考量下列範例查詢：</span><span class="sxs-lookup"><span data-stu-id="b9641-983">Consider the following example query:</span></span>

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

<span data-ttu-id="b9641-984">在此示例中,我們將許多可空變數與實體上的空屬性(如供應商 ID 和 UnitPrice)進行比較。</span><span class="sxs-lookup"><span data-stu-id="b9641-984">In this example, we’re comparing a number of nullable variables against nullable properties on the entity, such as SupplierID and UnitPrice.</span></span> <span data-ttu-id="b9641-985">此查詢生成的 SQL 將詢問參數值是否與列值相同,或者參數和列值是否為空。</span><span class="sxs-lookup"><span data-stu-id="b9641-985">The generated SQL for this query will ask if the parameter value is the same as the column value, or if both the parameter and the column values are null.</span></span> <span data-ttu-id="b9641-986">這將隱藏資料庫伺服器處理空的方式,並將跨不同的資料庫供應商提供一致的\#C null 體驗。</span><span class="sxs-lookup"><span data-stu-id="b9641-986">This will hide the way the database server handles nulls and will provide a consistent C\# null experience across different database vendors.</span></span> <span data-ttu-id="b9641-987">另一方面,生成的代碼有點複雜,當查詢語句中的比較量增長到大量時,可能無法很好地執行。</span><span class="sxs-lookup"><span data-stu-id="b9641-987">On the other hand, the generated code is a bit convoluted and may not perform well when the amount of comparisons in the where statement of the query grows to a large number.</span></span>

<span data-ttu-id="b9641-988">處理這種情況的一種方法是使用資料庫空語義。</span><span class="sxs-lookup"><span data-stu-id="b9641-988">One way to deal with this situation is by using database null semantics.</span></span> <span data-ttu-id="b9641-989">請注意,這可能與\#Cnull 語意的行為不同,因為現在實體框架將生成更簡單的 SQL,公開資料庫引擎處理空值的方式。</span><span class="sxs-lookup"><span data-stu-id="b9641-989">Note that this might potentially behave differently to the C\# null semantics since now Entity Framework will generate simpler SQL that exposes the way the database engine handles null values.</span></span> <span data-ttu-id="b9641-990">資料庫空語的義可以針對上下文設定使用單一個設定行啟動每個上下文:</span><span class="sxs-lookup"><span data-stu-id="b9641-990">Database null semantics can be activated per-context with one single configuration line against the context configuration:</span></span>

``` csharp
                context.Configuration.UseDatabaseNullSemantics = true;
```

<span data-ttu-id="b9641-991">使用資料庫空語義時,中小型查詢不會顯示可察覺的性能改進,但在具有大量潛在空比較的查詢上,差異將變得更加明顯。</span><span class="sxs-lookup"><span data-stu-id="b9641-991">Small to medium sized queries will not display a perceptible performance improvement when using database null semantics, but the difference will become more noticeable on queries with a large number of potential null comparisons.</span></span>

<span data-ttu-id="b9641-992">在上面的示例查詢中,在受控環境中運行的微基準中,性能差異小於 2%。</span><span class="sxs-lookup"><span data-stu-id="b9641-992">In the example query above, the performance difference was less than 2% in a microbenchmark running in a controlled environment.</span></span>

### <a name="95-async"></a><span data-ttu-id="b9641-993">9.5 同步</span><span class="sxs-lookup"><span data-stu-id="b9641-993">9.5      Async</span></span>

<span data-ttu-id="b9641-994">實體框架 6 在 .NET 4.5 或更高版本上運行時引入了對非同步操作的支援。</span><span class="sxs-lookup"><span data-stu-id="b9641-994">Entity Framework 6 introduced support of async operations when running on .NET 4.5 or later.</span></span> <span data-ttu-id="b9641-995">在大多數情況下,具有IO相關爭用的應用程式將從使用非同步查詢和保存操作中受益最大。</span><span class="sxs-lookup"><span data-stu-id="b9641-995">For the most part, applications that have IO related contention will benefit the most from using asynchronous query and save operations.</span></span> <span data-ttu-id="b9641-996">如果應用程式沒有受到 IO 爭用的影響,則在最好情況下,使用非同步將同步運行,並在與同步調用相同的時間內返回結果,或者在最壞的情況下,只需將執行推遲到異步任務,並添加額外的時間來完成您的方案。</span><span class="sxs-lookup"><span data-stu-id="b9641-996">If your application does not suffer from IO contention, the use of async will, in the best cases, run synchronously and return the result in the same amount of time as a synchronous call, or in the worst case, simply defer execution to an asynchronous task and add extra time to the completion of your scenario.</span></span>

<span data-ttu-id="b9641-997">有關非同步編程的工作原理的資訊,這將有助於您確定非同步程式是否會提高應用程式[http://msdn.microsoft.com/library/hh191443.aspx](https://msdn.microsoft.com/library/hh191443.aspx)存取的性能。</span><span class="sxs-lookup"><span data-stu-id="b9641-997">For information on how asynchronous programming work that will help you deciding if async will improve the performance of your application visit [http://msdn.microsoft.com/library/hh191443.aspx](https://msdn.microsoft.com/library/hh191443.aspx).</span></span> <span data-ttu-id="b9641-998">有關在實體框架上使用非同步操作的詳細資訊,請參閱[非同步查詢和儲存](~/ef6/fundamentals/async.md
)。</span><span class="sxs-lookup"><span data-stu-id="b9641-998">For more information on the use of async operations on Entity Framework, see [Async Query and Save](~/ef6/fundamentals/async.md
).</span></span>

### <a name="96-ngen"></a><span data-ttu-id="b9641-999">9.6 NGEN</span><span class="sxs-lookup"><span data-stu-id="b9641-999">9.6      NGEN</span></span>

<span data-ttu-id="b9641-1000">實體框架 6 不在 .NET 框架的預設安裝中。</span><span class="sxs-lookup"><span data-stu-id="b9641-1000">Entity Framework 6 does not come in the default installation of .NET framework.</span></span> <span data-ttu-id="b9641-1001">因此,默認情況下,實體框架程式集不是 NGEN d,這意味著所有實體框架代碼都受與任何其他 MSIL 程式集相同的 JIT 成本的約束。</span><span class="sxs-lookup"><span data-stu-id="b9641-1001">As such, the Entity Framework assemblies are not NGEN’d by default which means that all the Entity Framework code is subject to the same JIT’ing costs as any other MSIL assembly.</span></span> <span data-ttu-id="b9641-1002">這可能會降低 F5 在開發時的經驗,也會降低應用程式在生產環境中的冷啟動。</span><span class="sxs-lookup"><span data-stu-id="b9641-1002">This might degrade the F5 experience while developing and also the cold startup of your application in the production environments.</span></span> <span data-ttu-id="b9641-1003">為了降低 JIT 的 CPU 和記憶體成本,建議酌情使用實體框架映射進行 NGEN。</span><span class="sxs-lookup"><span data-stu-id="b9641-1003">In order to reduce the CPU and memory costs of JIT’ing it is advisable to NGEN the Entity Framework images as appropriate.</span></span> <span data-ttu-id="b9641-1004">有關如何使用 NGEN 提高實體框架 6 的啟動效能的詳細資訊,請參閱[使用 NGen 提高啟動性能](~/ef6/fundamentals/performance/ngen.md)。</span><span class="sxs-lookup"><span data-stu-id="b9641-1004">For more information on how to improve the startup performance of Entity Framework 6 with NGEN, see [Improving Startup Performance with NGen](~/ef6/fundamentals/performance/ngen.md).</span></span>

### <a name="97-code-first-versus-edmx"></a><span data-ttu-id="b9641-1005">9.7 程式碼優先與 EDMX</span><span class="sxs-lookup"><span data-stu-id="b9641-1005">9.7      Code First versus EDMX</span></span>

<span data-ttu-id="b9641-1006">實體框架通過具有概念模型(物件)、存儲架構(資料庫)和兩者之間映射的記憶體中表示形式,導致面向對象程式設計和關係資料庫之間的阻抗不匹配問題。</span><span class="sxs-lookup"><span data-stu-id="b9641-1006">Entity Framework reasons about the impedance mismatch problem between object oriented programming and relational databases by having an in-memory representation of the conceptual model (the objects), the storage schema (the database) and a mapping between the two.</span></span> <span data-ttu-id="b9641-1007">此元數據稱為實體數據模型,簡稱 EDM。</span><span class="sxs-lookup"><span data-stu-id="b9641-1007">This metadata is called an Entity Data Model, or EDM for short.</span></span> <span data-ttu-id="b9641-1008">從這個 EDM 中,實體框架將從記憶體中的物件派生到往返數據的視圖到資料庫並返回。</span><span class="sxs-lookup"><span data-stu-id="b9641-1008">From this EDM, Entity Framework will derive the views to roundtrip data from the objects in memory to the database and back.</span></span>

<span data-ttu-id="b9641-1009">當實體框架與正式指定概念模型、儲存架構和映射的 EDMX 檔一起使用時,模型載入階段只需驗證 EDM 是否正確(例如,確保沒有缺少映射),然後生成檢視,然後驗證視圖並準備好使用此元數據。</span><span class="sxs-lookup"><span data-stu-id="b9641-1009">When Entity Framework is used with an EDMX file that formally specifies the conceptual model, the storage schema, and the mapping, then the model loading stage only has to validate that the EDM is correct (for example, make sure that no mappings are missing), then generate the views, then validate the views and have this metadata ready for use.</span></span> <span data-ttu-id="b9641-1010">只有這樣,才能執行查詢或將新數據保存到數據存儲。</span><span class="sxs-lookup"><span data-stu-id="b9641-1010">Only then can a query be executed or new data be saved to the data store.</span></span>

<span data-ttu-id="b9641-1011">代碼優先方法的核心是一個複雜的實體數據模型生成器。</span><span class="sxs-lookup"><span data-stu-id="b9641-1011">The Code First approach is, at its heart, a sophisticated Entity Data Model generator.</span></span> <span data-ttu-id="b9641-1012">實體框架必須從提供的代碼生成 EDM;它通過分析模型中涉及的類、應用約定並通過 Fluent API 配置模型來達到這一要求。</span><span class="sxs-lookup"><span data-stu-id="b9641-1012">The Entity Framework has to produce an EDM from the provided code; it does so by analyzing the classes involved in the model, applying conventions and configuring the model via the Fluent API.</span></span> <span data-ttu-id="b9641-1013">構建 EDM 後,實體框架的工作方式與專案中存在 EDMX 檔的方式相同。</span><span class="sxs-lookup"><span data-stu-id="b9641-1013">After the EDM is built, the Entity Framework essentially behaves the same way as it would had an EDMX file been present in the project.</span></span> <span data-ttu-id="b9641-1014">因此,從 Code First 構建模型會增加額外的複雜性,與使用 EDMX 相比,實體框架的啟動時間會變慢。</span><span class="sxs-lookup"><span data-stu-id="b9641-1014">Thus, building the model from Code First adds extra complexity that translates into a slower startup time for the Entity Framework when compared to having an EDMX.</span></span> <span data-ttu-id="b9641-1015">成本完全取決於正在構建的模型的大小和複雜性。</span><span class="sxs-lookup"><span data-stu-id="b9641-1015">The cost is completely dependent on the size and complexity of the model that’s being built.</span></span>

<span data-ttu-id="b9641-1016">在選擇使用 EDMX 與代碼優先時,請務必瞭解代碼 First 引入的靈活性會增加首次構建模型的成本。</span><span class="sxs-lookup"><span data-stu-id="b9641-1016">When choosing to use EDMX versus Code First, it’s important to know that the flexibility introduced by Code First increases the cost of building the model for the first time.</span></span> <span data-ttu-id="b9641-1017">如果應用程式能夠承受此首次載入的成本,那麼通常 Code First 將是首選方式。</span><span class="sxs-lookup"><span data-stu-id="b9641-1017">If your application can withstand the cost of this first-time load then typically Code First will be the preferred way to go.</span></span>

## <a name="10-investigating-performance"></a><span data-ttu-id="b9641-1018">10 調查效能</span><span class="sxs-lookup"><span data-stu-id="b9641-1018">10 Investigating Performance</span></span>

### <a name="101-using-the-visual-studio-profiler"></a><span data-ttu-id="b9641-1019">10.1 使用視覺化工作室探查器</span><span class="sxs-lookup"><span data-stu-id="b9641-1019">10.1 Using the Visual Studio Profiler</span></span>

<span data-ttu-id="b9641-1020">如果實體框架存在性能問題,則可以使用 Visual Studio 中內置的探查器來查看應用程式花費的時間。</span><span class="sxs-lookup"><span data-stu-id="b9641-1020">If you are having performance issues with the Entity Framework, you can use a profiler like the one built into Visual Studio to see where your application is spending its time.</span></span> <span data-ttu-id="b9641-1021">這是我們用於在"探索ADO.NET實體框架的性能 - 第 1 部分\<https://docs.microsoft.com/archive/blogs/adonet/exploring-the-performance-of-the-ado-net-entity-framework-part-1>)"博客文章( 顯示實體框架在冷熱查詢期間花費時間)中生成餅圖的工具。</span><span class="sxs-lookup"><span data-stu-id="b9641-1021">This is the tool we used to generate the pie charts in the “Exploring the Performance of the ADO.NET Entity Framework - Part 1” blog post ( \<https://docs.microsoft.com/archive/blogs/adonet/exploring-the-performance-of-the-ado-net-entity-framework-part-1>) that show where Entity Framework spends its time during cold and warm queries.</span></span>

<span data-ttu-id="b9641-1022">數據和建模客戶諮詢團隊編寫的「使用 Visual Studio 2010 探查器分析實體框架」博客文章顯示了他們如何使用探查器調查性能問題的真實示例。</span><span class="sxs-lookup"><span data-stu-id="b9641-1022">The "Profiling Entity Framework using the Visual Studio 2010 Profiler" blog post written by the Data and Modeling Customer Advisory Team shows a real-world example of how they used the profiler to investigate a performance problem.</span></span><span data-ttu-id="b9641-1023">\<https://docs.microsoft.com/archive/blogs/dmcat/profiling-entity-framework-using-the-visual-studio-2010-profiler>.</span><span class="sxs-lookup"><span data-stu-id="b9641-1023">  \<https://docs.microsoft.com/archive/blogs/dmcat/profiling-entity-framework-using-the-visual-studio-2010-profiler>.</span></span> <span data-ttu-id="b9641-1024">這篇文章是為視窗應用程式編寫的。</span><span class="sxs-lookup"><span data-stu-id="b9641-1024">This post was written for a windows application.</span></span> <span data-ttu-id="b9641-1025">如果需要分析 Web 應用程式,Windows 性能記錄器 (WPR) 和 Windows 性能分析器 (WPA) 工具可能比在 Visual Studio 工作更好。</span><span class="sxs-lookup"><span data-stu-id="b9641-1025">If you need to profile a web application the Windows Performance Recorder (WPR) and Windows Performance Analyzer (WPA) tools may work better than working from Visual Studio.</span></span> <span data-ttu-id="b9641-1026">WPR 和 WPA 是 Windows 性能工具組的一部分,該工具[http://www.microsoft.com/download/details.aspx?id=39982](https://www.microsoft.com/download/details.aspx?id=39982)套件包含在 Windows 評估和部署工具組 () 中。</span><span class="sxs-lookup"><span data-stu-id="b9641-1026">WPR and WPA are part of the Windows Performance Toolkit which is included with the Windows Assessment and Deployment Kit ( [http://www.microsoft.com/download/details.aspx?id=39982](https://www.microsoft.com/download/details.aspx?id=39982)).</span></span>

### <a name="102-applicationdatabase-profiling"></a><span data-ttu-id="b9641-1027">10.2 應用程式/資料庫分析</span><span class="sxs-lookup"><span data-stu-id="b9641-1027">10.2 Application/Database profiling</span></span>

<span data-ttu-id="b9641-1028">像視覺化工作室中內建的探查器這樣的工具告訴您應用程式將花時間的位置。</span><span class="sxs-lookup"><span data-stu-id="b9641-1028">Tools like the profiler built into Visual Studio tell you where your application is spending time.</span></span><span data-ttu-id="b9641-1029">另一種類型的探查器可用於根據需求在生產或預生產中對正在運行的應用程序進行動態分析,並查找常見的陷阱和資料庫訪問的抗模式。</span><span class="sxs-lookup"><span data-stu-id="b9641-1029">  Another type of profiler is available that performs dynamic analysis of your running application, either in production or pre-production depending on needs, and looks for common pitfalls and anti-patterns of database access.</span></span>

<span data-ttu-id="b9641-1030">兩個市售的探查器是實體框架探查\<http://efprof.com>)器(和 ORMProfiler(。 \< http://ormprofiler.com>)</span><span class="sxs-lookup"><span data-stu-id="b9641-1030">Two commercially available profilers are the Entity Framework Profiler ( \<http://efprof.com>) and ORMProfiler ( \<http://ormprofiler.com>).</span></span>

<span data-ttu-id="b9641-1031">如果您的應用程式是使用代碼優先的 MVC 應用程式,則可以使用 StackExchange 的 Mini Profiler。</span><span class="sxs-lookup"><span data-stu-id="b9641-1031">If your application is an MVC application using Code First, you can use StackExchange's MiniProfiler.</span></span> <span data-ttu-id="b9641-1032">斯科特·漢塞爾曼在他的博客中描述了這個工具: \< http://www.hanselman.com/blog/NuGetPackageOfTheWeek9ASPNETMiniProfilerFromStackExchangeRocksYourWorld.aspx>.</span><span class="sxs-lookup"><span data-stu-id="b9641-1032">Scott Hanselman describes this tool in his blog at: \<http://www.hanselman.com/blog/NuGetPackageOfTheWeek9ASPNETMiniProfilerFromStackExchangeRocksYourWorld.aspx>.</span></span>

<span data-ttu-id="b9641-1033">有關分析應用程式的資料庫活動的詳細資訊,請參閱 Julie Lerman 的 MSDN 雜誌文章,標題為[實體框架中的分析資料庫活動](https://msdn.microsoft.com/magazine/gg490349.aspx)。</span><span class="sxs-lookup"><span data-stu-id="b9641-1033">For more information on profiling your application's database activity, see Julie Lerman's MSDN Magazine article titled [Profiling Database Activity in the Entity Framework](https://msdn.microsoft.com/magazine/gg490349.aspx).</span></span>

### <a name="103-database-logger"></a><span data-ttu-id="b9641-1034">10.3 資料庫記錄器</span><span class="sxs-lookup"><span data-stu-id="b9641-1034">10.3 Database logger</span></span>

<span data-ttu-id="b9641-1035">如果使用實體框架 6,還考慮使用內置日誌記錄功能。</span><span class="sxs-lookup"><span data-stu-id="b9641-1035">If you are using Entity Framework 6 also consider using the built-in logging functionality.</span></span> <span data-ttu-id="b9641-1036">可以指示上下文的資料庫屬性透過簡單的單行設定紀錄其活動:</span><span class="sxs-lookup"><span data-stu-id="b9641-1036">The Database property of the context can be instructed to log its activity via a simple one-line configuration:</span></span>

``` csharp
    using (var context = newQueryComparison.DbC.NorthwindEntities())
    {
        context.Database.Log = Console.WriteLine;
        var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
        q.ToList();
    }
```

<span data-ttu-id="b9641-1037">在此示例中,資料庫活動將記錄到主控台,但可以將 Log 屬性配置為調&lt;用任何操作&gt;字串 委託。</span><span class="sxs-lookup"><span data-stu-id="b9641-1037">In this example the database activity will be logged to the console, but the Log property can be configured to call any Action&lt;string&gt; delegate.</span></span>

<span data-ttu-id="b9641-1038">如果要在不重新編譯的情況下啟用資料庫日誌記錄,並且正在使用實體框架 6.1 或更高版本,則可以通過在應用程式的 Web.config 或 app.config 檔中添加攔截器來執行此操作。</span><span class="sxs-lookup"><span data-stu-id="b9641-1038">If you want to enable database logging without recompiling, and you are using Entity Framework 6.1 or later, you can do so by adding an interceptor in the web.config or app.config file of your application.</span></span>

``` xml
  <interceptors>
    <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework">
      <parameters>
        <parameter value="C:\Path\To\My\LogOutput.txt"/>
      </parameters>
    </interceptor>
  </interceptors>
```

<span data-ttu-id="b9641-1039">有關如何在不重新編譯的情況下加入紀錄紀錄的詳細資訊,\<http://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/>請存取 。</span><span class="sxs-lookup"><span data-stu-id="b9641-1039">For more information on how to add logging without recompiling go to \<http://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/>.</span></span>

## <a name="11-appendix"></a><span data-ttu-id="b9641-1040">11 附錄</span><span class="sxs-lookup"><span data-stu-id="b9641-1040">11 Appendix</span></span>

### <a name="111-a-test-environment"></a><span data-ttu-id="b9641-1041">11.1 A. 測試環境</span><span class="sxs-lookup"><span data-stu-id="b9641-1041">11.1 A. Test Environment</span></span>

<span data-ttu-id="b9641-1042">此環境使用2台電腦設置,資料庫與用戶端應用程式分開的電腦上。</span><span class="sxs-lookup"><span data-stu-id="b9641-1042">This environment uses a 2-machine setup with the database on a separate machine from the client application.</span></span> <span data-ttu-id="b9641-1043">計算機位於同一機架中,因此網路延遲相對較低,但比單機環境更逼真。</span><span class="sxs-lookup"><span data-stu-id="b9641-1043">Machines are in the same rack, so network latency is relatively low, but more realistic than a single-machine environment.</span></span>

#### <a name="1111-app-server"></a><span data-ttu-id="b9641-1044">11.1.1 應用伺服器</span><span class="sxs-lookup"><span data-stu-id="b9641-1044">11.1.1       App Server</span></span>

##### <a name="11111-software-environment"></a><span data-ttu-id="b9641-1045">11.1.1.1 軟體環境</span><span class="sxs-lookup"><span data-stu-id="b9641-1045">11.1.1.1      Software Environment</span></span>

-   <span data-ttu-id="b9641-1046">實體框架 4 軟體環境</span><span class="sxs-lookup"><span data-stu-id="b9641-1046">Entity Framework 4 Software Environment</span></span>
    -   <span data-ttu-id="b9641-1047">操作系統名稱:Windows 伺服器 2008 R2 企業 SP1。</span><span class="sxs-lookup"><span data-stu-id="b9641-1047">OS Name: Windows Server 2008 R2 Enterprise SP1.</span></span>
    -   <span data-ttu-id="b9641-1048">視覺工作室 2010 – 終極。</span><span class="sxs-lookup"><span data-stu-id="b9641-1048">Visual Studio 2010 – Ultimate.</span></span>
    -   <span data-ttu-id="b9641-1049">可視化工作室 2010 SP1 (僅適用於某些比較)。</span><span class="sxs-lookup"><span data-stu-id="b9641-1049">Visual Studio 2010 SP1 (only for some comparisons).</span></span>
-   <span data-ttu-id="b9641-1050">實體框架 5 和 6 軟體環境</span><span class="sxs-lookup"><span data-stu-id="b9641-1050">Entity Framework 5 and 6 Software Environment</span></span>
    -   <span data-ttu-id="b9641-1051">操作系統名稱:Windows 8.1 企業版</span><span class="sxs-lookup"><span data-stu-id="b9641-1051">OS Name: Windows 8.1 Enterprise</span></span>
    -   <span data-ttu-id="b9641-1052">視覺工作室 2013 – 終極。</span><span class="sxs-lookup"><span data-stu-id="b9641-1052">Visual Studio 2013 – Ultimate.</span></span>

##### <a name="11112-hardware-environment"></a><span data-ttu-id="b9641-1053">11.1.1.2 硬體環境</span><span class="sxs-lookup"><span data-stu-id="b9641-1053">11.1.1.2      Hardware Environment</span></span>

-   <span data-ttu-id="b9641-1054">雙處理器:英特爾(R) Xeon(R) CPU L5520 W3530 = 2.27GHz,2261 Mhz8 GHz,4 核(s),84 邏輯處理器。</span><span class="sxs-lookup"><span data-stu-id="b9641-1054">Dual Processor:     Intel(R) Xeon(R) CPU L5520 W3530 @ 2.27GHz, 2261 Mhz8 GHz, 4 Core(s), 84 Logical Processor(s).</span></span>
-   <span data-ttu-id="b9641-1055">2412 GB RamRAM。</span><span class="sxs-lookup"><span data-stu-id="b9641-1055">2412 GB RamRAM.</span></span>
-   <span data-ttu-id="b9641-1056">136 GB SCSI250GB SATA 7200 rpm 3GB/s 驅動器拆分為 4 個分區。</span><span class="sxs-lookup"><span data-stu-id="b9641-1056">136 GB SCSI250GB SATA 7200 rpm 3GB/s drive split into 4 partitions.</span></span>

#### <a name="1112-db-server"></a><span data-ttu-id="b9641-1057">11.1.2 資料庫伺服器</span><span class="sxs-lookup"><span data-stu-id="b9641-1057">11.1.2       DB server</span></span>

##### <a name="11121-software-environment"></a><span data-ttu-id="b9641-1058">11.1.2.1 軟體環境</span><span class="sxs-lookup"><span data-stu-id="b9641-1058">11.1.2.1      Software Environment</span></span>

-   <span data-ttu-id="b9641-1059">操作系統名稱:Windows 伺服器 2008 R28.1 企業 SP1。</span><span class="sxs-lookup"><span data-stu-id="b9641-1059">OS Name: Windows Server 2008 R28.1 Enterprise SP1.</span></span>
-   <span data-ttu-id="b9641-1060">SQL 伺服器 2008 R22012。</span><span class="sxs-lookup"><span data-stu-id="b9641-1060">SQL Server 2008 R22012.</span></span>

##### <a name="11122-hardware-environment"></a><span data-ttu-id="b9641-1061">11.1.2.2 硬體環境</span><span class="sxs-lookup"><span data-stu-id="b9641-1061">11.1.2.2      Hardware Environment</span></span>

-   <span data-ttu-id="b9641-1062">單處理器:英特爾(R) Xeon(R) CPU L5520 = 2.27GHz,2261 MhzES-1620 0 = 3.60GHz,4 個核心(s),8 個邏輯處理器。</span><span class="sxs-lookup"><span data-stu-id="b9641-1062">Single Processor: Intel(R) Xeon(R) CPU L5520  @ 2.27GHz, 2261 MhzES-1620 0 @ 3.60GHz, 4 Core(s), 8 Logical Processor(s).</span></span>
-   <span data-ttu-id="b9641-1063">824 GB 拉姆拉姆。</span><span class="sxs-lookup"><span data-stu-id="b9641-1063">824 GB RamRAM.</span></span>
-   <span data-ttu-id="b9641-1064">465 GB ATA500GB SATA 7200 rpm 6GB/s 驅動器拆分為 4 個分區。</span><span class="sxs-lookup"><span data-stu-id="b9641-1064">465 GB ATA500GB SATA 7200 rpm 6GB/s drive split into 4 partitions.</span></span>

### <a name="112-b-query-performance-comparison-tests"></a><span data-ttu-id="b9641-1065">11.2 B. 查詢效能比較測試</span><span class="sxs-lookup"><span data-stu-id="b9641-1065">11.2      B. Query performance comparison tests</span></span>

<span data-ttu-id="b9641-1066">北風模型用於執行這些測試。</span><span class="sxs-lookup"><span data-stu-id="b9641-1066">The Northwind model was used to execute these tests.</span></span> <span data-ttu-id="b9641-1067">它是使用實體框架設計器從資料庫生成的。</span><span class="sxs-lookup"><span data-stu-id="b9641-1067">It was generated from the database using the Entity Framework designer.</span></span> <span data-ttu-id="b9641-1068">然後,使用以下代碼比較查詢執行選項的效能:</span><span class="sxs-lookup"><span data-stu-id="b9641-1068">Then, the following code was used to compare the performance of the query execution options:</span></span>

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

### <a name="113-c-navision-model"></a><span data-ttu-id="b9641-1069">11.3 C. 納維視覺模型</span><span class="sxs-lookup"><span data-stu-id="b9641-1069">11.3 C. Navision Model</span></span>

<span data-ttu-id="b9641-1070">Navision 資料庫是一個大型資料庫,用於演示 Microsoft 動態和導航。</span><span class="sxs-lookup"><span data-stu-id="b9641-1070">The Navision database is a large database used to demo Microsoft Dynamics – NAV.</span></span> <span data-ttu-id="b9641-1071">生成的概念模型包含 1005 個實體集和 4227 個關聯集。</span><span class="sxs-lookup"><span data-stu-id="b9641-1071">The generated conceptual model contains 1005 entity sets and 4227 association sets.</span></span> <span data-ttu-id="b9641-1072">測試中使用的模型為「平面」 - 未向其添加繼承。</span><span class="sxs-lookup"><span data-stu-id="b9641-1072">The model used in the test is “flat” – no inheritance has been added to it.</span></span>

#### <a name="1131-queries-used-for-navision-tests"></a><span data-ttu-id="b9641-1073">11.3.1 用於 Navision 測試的查詢</span><span class="sxs-lookup"><span data-stu-id="b9641-1073">11.3.1 Queries used for Navision tests</span></span>

<span data-ttu-id="b9641-1074">與 Navision 模型一起使用的查詢清單包含 3 類別 SQL 查詢:</span><span class="sxs-lookup"><span data-stu-id="b9641-1074">The queries list used with the Navision model contains 3 categories of Entity SQL queries:</span></span>

##### <a name="11311-lookup"></a><span data-ttu-id="b9641-1075">11.3.1.1 查找</span><span class="sxs-lookup"><span data-stu-id="b9641-1075">11.3.1.1 Lookup</span></span>

<span data-ttu-id="b9641-1076">沒有聚合的簡單尋找查詢</span><span class="sxs-lookup"><span data-stu-id="b9641-1076">A simple lookup query with no aggregations</span></span>

-   <span data-ttu-id="b9641-1077">數量: 16232</span><span class="sxs-lookup"><span data-stu-id="b9641-1077">Count: 16232</span></span>
-   <span data-ttu-id="b9641-1078">範例：</span><span class="sxs-lookup"><span data-stu-id="b9641-1078">Example:</span></span>

``` xml
  <Query complexity="Lookup">
    <CommandText>Select value distinct top(4) e.Idle_Time From NavisionFKContext.Session as e</CommandText>
  </Query>
```

##### <a name="11312singleaggregating"></a><span data-ttu-id="b9641-1079">11.3.1.2 單聚合</span><span class="sxs-lookup"><span data-stu-id="b9641-1079">11.3.1.2 SingleAggregating</span></span>

<span data-ttu-id="b9641-1080">具有多個聚合但沒有子值(單一查詢)的普通 BI 查詢</span><span class="sxs-lookup"><span data-stu-id="b9641-1080">A normal BI query with multiple aggregations, but no subtotals (single query)</span></span>

-   <span data-ttu-id="b9641-1081">數量: 2313</span><span class="sxs-lookup"><span data-stu-id="b9641-1081">Count: 2313</span></span>
-   <span data-ttu-id="b9641-1082">範例：</span><span class="sxs-lookup"><span data-stu-id="b9641-1082">Example:</span></span>

``` xml
  <Query complexity="SingleAggregating">
    <CommandText>NavisionFK.MDF_SessionLogin_Time_Max()</CommandText>
  </Query>
```

<span data-ttu-id="b9641-1083">其中 MDF\_\_\_工作階段 登入時間最大值() 在模型中定義為:</span><span class="sxs-lookup"><span data-stu-id="b9641-1083">Where MDF\_SessionLogin\_Time\_Max() is defined in the model as:</span></span>

``` xml
  <Function Name="MDF_SessionLogin_Time_Max" ReturnType="Collection(DateTime)">
    <DefiningExpression>SELECT VALUE Edm.Min(E.Login_Time) FROM NavisionFKContext.Session as E</DefiningExpression>
  </Function>
```

##### <a name="11313aggregatingsubtotals"></a><span data-ttu-id="b9641-1084">11.3.1.3 聚合子總計</span><span class="sxs-lookup"><span data-stu-id="b9641-1084">11.3.1.3 AggregatingSubtotals</span></span>

<span data-ttu-id="b9641-1085">包含聚合和小計的 BI 查詢(透過所有聯合)</span><span class="sxs-lookup"><span data-stu-id="b9641-1085">A BI query with aggregations and subtotals (via union all)</span></span>

-   <span data-ttu-id="b9641-1086">計數: 178</span><span class="sxs-lookup"><span data-stu-id="b9641-1086">Count: 178</span></span>
-   <span data-ttu-id="b9641-1087">範例：</span><span class="sxs-lookup"><span data-stu-id="b9641-1087">Example:</span></span>

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
