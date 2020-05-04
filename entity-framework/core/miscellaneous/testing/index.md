---
title: 測試使用 EF Core 的程式碼 - EF Core
description: 測試使用 EF Core 的應用程式不同方法
author: ajcvickers
ms.date: 04/22/2020
uid: core/miscellaneous/testing/index
ms.openlocfilehash: 308128b0d51b9e0d1fc1ebb0ed00e803100efb52
ms.sourcegitcommit: 79e460f76b6664e1da5886d102bd97f651d2ffff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82538356"
---
# <a name="testing-code-that-uses-ef-core"></a><span data-ttu-id="9ca6a-103">測試使用 EF Core 的程式碼</span><span class="sxs-lookup"><span data-stu-id="9ca6a-103">Testing code that uses EF Core</span></span>

<span data-ttu-id="9ca6a-104">測試存取資料庫的程式碼需要下列其中一項：</span><span class="sxs-lookup"><span data-stu-id="9ca6a-104">Testing code that accesses a database requires either:</span></span>
* <span data-ttu-id="9ca6a-105">對生產環境中所使用的相同資料庫系統執行查詢和更新。</span><span class="sxs-lookup"><span data-stu-id="9ca6a-105">Running queries and updates against the same database system used in production.</span></span>
* <span data-ttu-id="9ca6a-106">對其他一些較容易管理的資料庫系統執行查詢和更新。</span><span class="sxs-lookup"><span data-stu-id="9ca6a-106">Running queries and updates against some other easier to manage database system.</span></span>
* <span data-ttu-id="9ca6a-107">使用 test double 或其他一些機制來完全避免使用資料庫。</span><span class="sxs-lookup"><span data-stu-id="9ca6a-107">Using test doubles or some other mechanism to avoid using a database at all.</span></span>

<span data-ttu-id="9ca6a-108">本文件概述上述每種選擇所牽涉到的取捨，並示範 EF Core 如何搭配每種方法使用。</span><span class="sxs-lookup"><span data-stu-id="9ca6a-108">This document outlines the trade offs involved in each of these choices and shows how EF Core can be used with each approach.</span></span>  

> [!TIP]
> <span data-ttu-id="9ca6a-109">請參閱 [EF Core 測試範例](xref:core/miscellaneous/testing/testing-sample)來取得示範這裡所介紹之概念的程式碼。</span><span class="sxs-lookup"><span data-stu-id="9ca6a-109">See [EF Core testing sample](xref:core/miscellaneous/testing/testing-sample) for code demonstrating the concepts introduced here.</span></span> 

## <a name="all-database-providers-are-not-equal"></a><span data-ttu-id="9ca6a-110">所有資料庫提供者都不相同</span><span class="sxs-lookup"><span data-stu-id="9ca6a-110">All database providers are not equal</span></span>

<span data-ttu-id="9ca6a-111">請務必了解，EF Core 的設計不是將基礎資料庫系統的每個層面抽象化。</span><span class="sxs-lookup"><span data-stu-id="9ca6a-111">It is very important to understand that EF Core is not designed to abstract every aspect of the underlying database system.</span></span>
<span data-ttu-id="9ca6a-112">相反地，EF Core 是一組通用的模式和概念，可搭配任何資料庫系統使用。</span><span class="sxs-lookup"><span data-stu-id="9ca6a-112">Instead, EF Core is a common set of patterns and concepts that can be used with any database system.</span></span>
<span data-ttu-id="9ca6a-113">EF Core 資料庫提供者接著會在此通用架構上分層資料庫特定的行為和功能。</span><span class="sxs-lookup"><span data-stu-id="9ca6a-113">EF Core database providers then layer database-specific behavior and functionality over this common framework.</span></span>
<span data-ttu-id="9ca6a-114">這讓每個資料庫系統得以發揮所長，同時仍可在適當的情況下，與其他資料庫系統保持共通性。</span><span class="sxs-lookup"><span data-stu-id="9ca6a-114">This allows each database system to do what it does best while still maintaining commonality, where appropriate, with other database systems.</span></span> 

<span data-ttu-id="9ca6a-115">基本上，這表示交換資料庫提供者將會變更 EF Core 行為，且應用程式可能無法正常運作，除非其明確地將任何行為上的差異納入考量。</span><span class="sxs-lookup"><span data-stu-id="9ca6a-115">Fundamentally, this means that switching out the database provider will change EF Core behavior and the application can't be expected to function correctly unless it explicitly accounts for any differences in behavior.</span></span>
<span data-ttu-id="9ca6a-116">話雖如此，在許多情況下，由於關聯式資料庫之間有高度的共通性，因此這樣做沒有問題。</span><span class="sxs-lookup"><span data-stu-id="9ca6a-116">That being said, in many cases doing this will work because there is a high degree of commonality amongst relational databases.</span></span>
<span data-ttu-id="9ca6a-117">這有好處也有壞處。</span><span class="sxs-lookup"><span data-stu-id="9ca6a-117">This is good and bad.</span></span>
<span data-ttu-id="9ca6a-118">好處是在資料庫系統之間移動可能相當容易。</span><span class="sxs-lookup"><span data-stu-id="9ca6a-118">Good because moving between database systems can be relatively easy.</span></span>
<span data-ttu-id="9ca6a-119">壞處是如果應用程式未針對新的資料庫系統進行完整測試，可能會對安全性有所誤判。</span><span class="sxs-lookup"><span data-stu-id="9ca6a-119">Bad because it can give a false sense of security if the application is not fully tested against the new database system.</span></span>  

## <a name="approach-1-production-database-system"></a><span data-ttu-id="9ca6a-120">方法 1：生產資料庫系統</span><span class="sxs-lookup"><span data-stu-id="9ca6a-120">Approach 1: Production database system</span></span>

<span data-ttu-id="9ca6a-121">如上一節所述，確定測試就是生產環境中執行內容的唯一方式，就是使用相同的資料庫系統。</span><span class="sxs-lookup"><span data-stu-id="9ca6a-121">As described in the previous section, the only way to be sure you are testing what runs in production is to use the same database system.</span></span>
<span data-ttu-id="9ca6a-122">例如，如果部署的應用程式使用 SQL Azure，則也應該對 SQL Azure 進行測試。</span><span class="sxs-lookup"><span data-stu-id="9ca6a-122">For example, if the deployed application uses SQL Azure, then testing should also be done against SQL Azure.</span></span>

<span data-ttu-id="9ca6a-123">不過，讓所有開發人員對 SQL Azure 執行測試，同時積極地開發程式碼，不僅速度緩慢，也很耗費成本。</span><span class="sxs-lookup"><span data-stu-id="9ca6a-123">However, having every developer run tests against SQL Azure while actively working on the code would be both slow and expensive.</span></span>
<span data-ttu-id="9ca6a-124">這說明這些方法所牽涉到的主要取捨：何時適合偏離生產資料庫系統，以提升測試效率？</span><span class="sxs-lookup"><span data-stu-id="9ca6a-124">This illustrates the main trade off involved throughout these approaches: when is it appropriate to deviate from the production database system so as to improve test efficiency?</span></span>

<span data-ttu-id="9ca6a-125">幸運的是，在此情況下，答案相當簡單：使用本機或內部部署 SQL Server 進行開發人員測試。</span><span class="sxs-lookup"><span data-stu-id="9ca6a-125">Luckily, in this case the answer is quite easy: use local or on-premises SQL Server for developer testing.</span></span>
<span data-ttu-id="9ca6a-126">SQL Azure 和 SQL Server 非常類似，因此對 SQL Server 進行測試通常是合理的取捨。</span><span class="sxs-lookup"><span data-stu-id="9ca6a-126">SQL Azure and SQL Server are extremely similar, so testing against SQL Server is usually a reasonable trade off.</span></span>
<span data-ttu-id="9ca6a-127">話雖如此，最好還是在進入生產環境之前，先對 SQL Azure 本身執行測試。</span><span class="sxs-lookup"><span data-stu-id="9ca6a-127">That being said, it is still wise to run tests against SQL Azure itself before going into production.</span></span>
 
### <a name="localdb"></a><span data-ttu-id="9ca6a-128">LocalDB</span><span class="sxs-lookup"><span data-stu-id="9ca6a-128">LocalDB</span></span> 

<span data-ttu-id="9ca6a-129">所有主要資料庫系統都有某種形式的「開發人員版本」，可用於本機測試。</span><span class="sxs-lookup"><span data-stu-id="9ca6a-129">All the major database systems have some form of "Developer Edition" for local testing.</span></span>
<span data-ttu-id="9ca6a-130">SQL Server 也有稱為 [LocalDB](/sql/database-engine/configure-windows/sql-server-express-localdb?view=sql-server-ver15) 的功能。</span><span class="sxs-lookup"><span data-stu-id="9ca6a-130">SQL Server also has a feature called [LocalDB](/sql/database-engine/configure-windows/sql-server-express-localdb?view=sql-server-ver15).</span></span>
<span data-ttu-id="9ca6a-131">LocalDB 的主要優點在於會視需要啟動資料庫執行個體。</span><span class="sxs-lookup"><span data-stu-id="9ca6a-131">The primary advantage of LocalDB is that it spins up the database instance on demand.</span></span>
<span data-ttu-id="9ca6a-132">這可避免在電腦上執行資料庫服務，即使未正在執行測試也一樣。</span><span class="sxs-lookup"><span data-stu-id="9ca6a-132">This avoids having a database service running on your machine even when you're not running tests.</span></span>

<span data-ttu-id="9ca6a-133">但 LocalDB 也有自身的問題：</span><span class="sxs-lookup"><span data-stu-id="9ca6a-133">LocalDB is not without it's issues:</span></span>
* <span data-ttu-id="9ca6a-134">它無法提供 [SQL Server Developer Edition](/sql/sql-server/editions-and-components-of-sql-server-2016?view=sql-server-ver15) 的所有支援。</span><span class="sxs-lookup"><span data-stu-id="9ca6a-134">It doesn't support everything that [SQL Server Developer Edition](/sql/sql-server/editions-and-components-of-sql-server-2016?view=sql-server-ver15) does.</span></span>
* <span data-ttu-id="9ca6a-135">不適用於 Linux。</span><span class="sxs-lookup"><span data-stu-id="9ca6a-135">It isn't available on Linux.</span></span>
* <span data-ttu-id="9ca6a-136">啟動服務時，可能會造成第一個測試回合延遲。</span><span class="sxs-lookup"><span data-stu-id="9ca6a-136">It can cause lag on first test run as the service is spun up.</span></span>

<span data-ttu-id="9ca6a-137">我個人從不認為在開發電腦上執行資料庫服務有何問題，因此通常會建議改用 Developer Edition。</span><span class="sxs-lookup"><span data-stu-id="9ca6a-137">Personally, I've never found it a problem having a database service running on my dev machine and I would generally recommend using Developer Edition instead.</span></span>
<span data-ttu-id="9ca6a-138">不過，LocalDB 可能適合某些人，特別是在功能不強大的開發電腦上。</span><span class="sxs-lookup"><span data-stu-id="9ca6a-138">However, LocalDB may be appropriate for some people, especially on less powerful dev machines.</span></span>

<span data-ttu-id="9ca6a-139">在 Docker 容器 (或類似容器) 中執行 SQL Server (或任何其他資料庫系統)，是另一種能夠避免直接在開發電腦上執行資料庫系統的方式。</span><span class="sxs-lookup"><span data-stu-id="9ca6a-139">Running SQL Server (or any other database system) in a Docker container (os similar) is another way to avoid running the database system directly on your development machine.</span></span>  

## <a name="approach-2-sqlite"></a><span data-ttu-id="9ca6a-140">方法 2：SQLite</span><span class="sxs-lookup"><span data-stu-id="9ca6a-140">Approach 2: SQLite</span></span>

<span data-ttu-id="9ca6a-141">EF Core 測試 SQL Server 提供者的方式，主要是透過對本機 SQL Server 執行個體執行測試。</span><span class="sxs-lookup"><span data-stu-id="9ca6a-141">EF Core tests the SQL Server provider primarily by running it against a local SQL Server instance.</span></span>
<span data-ttu-id="9ca6a-142">在快速的電腦上，這些測試會在幾分鐘內執行數十萬個查詢。</span><span class="sxs-lookup"><span data-stu-id="9ca6a-142">These tests run tens of thousands of queries in a couple of minutes on a fast machine.</span></span>
<span data-ttu-id="9ca6a-143">這說明使用實際資料庫系統可能是高效能的解決方案。</span><span class="sxs-lookup"><span data-stu-id="9ca6a-143">This illustrates that using the real database system can be a performant solution.</span></span>
<span data-ttu-id="9ca6a-144">這是個迷思，認為使用較輕量型資料庫是快速執行測試的唯一方式。</span><span class="sxs-lookup"><span data-stu-id="9ca6a-144">It is a myth that using some lighter-weight database is the only way to run tests quickly.</span></span>

<span data-ttu-id="9ca6a-145">話雖如此，萬一無法對接近生產資料庫系統的系統執行測試，那該怎麼辦？</span><span class="sxs-lookup"><span data-stu-id="9ca6a-145">That being said, what if for whatever reason you can't run tests against something close to your production database system?</span></span>
<span data-ttu-id="9ca6a-146">次佳選擇是使用具有類似功能的產品。</span><span class="sxs-lookup"><span data-stu-id="9ca6a-146">The next best choice is to use something with similar functionality.</span></span>
<span data-ttu-id="9ca6a-147">這通常表示另一個關聯式資料庫，而 [SQLite](https://sqlite.org/index.html) 是明顯的選擇。</span><span class="sxs-lookup"><span data-stu-id="9ca6a-147">This usually means another relational database, for which [SQLite](https://sqlite.org/index.html) is the obvious choice.</span></span>

<span data-ttu-id="9ca6a-148">SQLite 是不錯的選擇，因為：</span><span class="sxs-lookup"><span data-stu-id="9ca6a-148">SQLite is a good choice because:</span></span>
* <span data-ttu-id="9ca6a-149">它與應用程式同處理序執行，因此額外負荷很低。</span><span class="sxs-lookup"><span data-stu-id="9ca6a-149">It runs in-process with your application and so has low overhead.</span></span>
* <span data-ttu-id="9ca6a-150">使用簡單且自動為資料庫建立的檔案，因此不需要資料庫管理。</span><span class="sxs-lookup"><span data-stu-id="9ca6a-150">It uses simple, automatically created files for databases, and so doesn't require database management.</span></span>
* <span data-ttu-id="9ca6a-151">具有記憶體內部模式，可避免甚至是建立檔案。</span><span class="sxs-lookup"><span data-stu-id="9ca6a-151">It has an in-memory mode that avoids even the file creation.</span></span>

<span data-ttu-id="9ca6a-152">不過，請記住：</span><span class="sxs-lookup"><span data-stu-id="9ca6a-152">However, remember that:</span></span>
* <span data-ttu-id="9ca6a-153">SQLite 必然無法提供生產資料庫系統的所有支援。</span><span class="sxs-lookup"><span data-stu-id="9ca6a-153">SQLite inevitability doesn't support everything that your production database system does.</span></span>
* <span data-ttu-id="9ca6a-154">對於某些查詢，SQLite 的運作方式會不同於生產資料庫系統。</span><span class="sxs-lookup"><span data-stu-id="9ca6a-154">SQLite will behave differently than your production database system for some queries.</span></span>

<span data-ttu-id="9ca6a-155">因此，如果使用 SQLite 進行一些測試，請務必同時對實際資料庫系統進行測試。</span><span class="sxs-lookup"><span data-stu-id="9ca6a-155">So if you do use SQLite for some testing, make sure to also test against your real database system.</span></span>

<span data-ttu-id="9ca6a-156">如需 EF Core 特定指導方針，請參閱[使用 SQLite 進行測試](xref:core/miscellaneous/testing/sqlite)。</span><span class="sxs-lookup"><span data-stu-id="9ca6a-156">See [Testing with SQLite](xref:core/miscellaneous/testing/sqlite) for EF Core specific guidance.</span></span> 

## <a name="approach-3-the-ef-core-in-memory-database"></a><span data-ttu-id="9ca6a-157">方法 3：EF Core 記憶體內部資料庫</span><span class="sxs-lookup"><span data-stu-id="9ca6a-157">Approach 3: The EF Core in-memory database</span></span>

<span data-ttu-id="9ca6a-158">EF Core 隨附記憶體內部資料庫，可用於 EF Core 本身的內部測試。</span><span class="sxs-lookup"><span data-stu-id="9ca6a-158">EF Core comes with an in-memory database that we use for internal testing of EF Core itself.</span></span>
<span data-ttu-id="9ca6a-159">此資料庫一般**不適合作為測試使用 EF Core 的應用程式替代方法**。</span><span class="sxs-lookup"><span data-stu-id="9ca6a-159">This database is in general **not suitable as a substitute for testing applications that use EF Core**.</span></span> <span data-ttu-id="9ca6a-160">尤其是：</span><span class="sxs-lookup"><span data-stu-id="9ca6a-160">Specifically:</span></span>
* <span data-ttu-id="9ca6a-161">這不是關聯式資料庫</span><span class="sxs-lookup"><span data-stu-id="9ca6a-161">It is not a relational database</span></span>
* <span data-ttu-id="9ca6a-162">不支援交易</span><span class="sxs-lookup"><span data-stu-id="9ca6a-162">It doesn't support transactions</span></span>
* <span data-ttu-id="9ca6a-163">未針對效能最佳化</span><span class="sxs-lookup"><span data-stu-id="9ca6a-163">It is not optimized for performance</span></span>

<span data-ttu-id="9ca6a-164">當測試 EF Core 內部時，上述幾點並不重要，因為我們會將其特別用於資料庫與測試無關的情況下。</span><span class="sxs-lookup"><span data-stu-id="9ca6a-164">None of this is very important when testing EF Core internals because we use it specifically where the database is irrelevant to the test.</span></span>
<span data-ttu-id="9ca6a-165">另一方面，當測試使用 EF Core 的應用程式時，上述幾點則通常非常重要。</span><span class="sxs-lookup"><span data-stu-id="9ca6a-165">On the other hand, these things tend to be very important when testing an application that uses EF Core.</span></span>

## <a name="unit-testing"></a><span data-ttu-id="9ca6a-166">單元測試</span><span class="sxs-lookup"><span data-stu-id="9ca6a-166">Unit testing</span></span>

<span data-ttu-id="9ca6a-167">請考慮測試可能需要使用資料庫中某些資料，但不會因此測試資料庫互動的商務邏輯。</span><span class="sxs-lookup"><span data-stu-id="9ca6a-167">Consider testing a piece of business logic that might need to use some data from a database, but is not inherently testing the database interactions.</span></span>
<span data-ttu-id="9ca6a-168">其中一個選項是使用 [test double](https://en.wikipedia.org/wiki/Test_double)，例如 mock 或 fake。</span><span class="sxs-lookup"><span data-stu-id="9ca6a-168">One option is to use a [test double](https://en.wikipedia.org/wiki/Test_double) such as a mock or fake.</span></span>

<span data-ttu-id="9ca6a-169">我們使用 test double 進行 EF Core 的內部測試。</span><span class="sxs-lookup"><span data-stu-id="9ca6a-169">We use test doubles for internal testing of EF Core.</span></span>
<span data-ttu-id="9ca6a-170">不過，我們絕對不會嘗試 mock DbContext 或 IQueryable。</span><span class="sxs-lookup"><span data-stu-id="9ca6a-170">However, we never try to mock DbContext or IQueryable.</span></span>
<span data-ttu-id="9ca6a-171">這麼做很困難、繁瑣且容易出錯。</span><span class="sxs-lookup"><span data-stu-id="9ca6a-171">Doing so is difficult, cumbersome, and fragile.</span></span>
<span data-ttu-id="9ca6a-172">**請不要這麼做。**</span><span class="sxs-lookup"><span data-stu-id="9ca6a-172">**Don't do it.**</span></span>

<span data-ttu-id="9ca6a-173">相反地，對使用 DbCoNtext 的項目進行單元測試時，我們會使用 EF 記憶體內部資料庫。</span><span class="sxs-lookup"><span data-stu-id="9ca6a-173">Instead we use the EF in-memory database when unit testing something that uses DbContext.</span></span>
<span data-ttu-id="9ca6a-174">在此情況下，由於測試不會受到資料庫行為影響，因此適合使用 EF 記憶體內部資料庫。</span><span class="sxs-lookup"><span data-stu-id="9ca6a-174">In this case using the EF in-memory database is appropriate because the test is not dependent on database behavior.</span></span>
<span data-ttu-id="9ca6a-175">只要別用來測試實際資料庫查詢或更新即可。</span><span class="sxs-lookup"><span data-stu-id="9ca6a-175">Just don't do this to test actual database queries or updates.</span></span>   

<span data-ttu-id="9ca6a-176">[EF Core 測試範例](xref:core/miscellaneous/testing/testing-sample)會示範使用 EF 記憶體內部資料庫，以及 SQL Server 和 SQLite 的測試。</span><span class="sxs-lookup"><span data-stu-id="9ca6a-176">The [EF Core testing sample](xref:core/miscellaneous/testing/testing-sample) demonstrates tests using the EF in-memory database, as well as SQL Server and SQLite.</span></span> 
