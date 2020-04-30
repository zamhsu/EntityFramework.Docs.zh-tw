---
title: 在測試之間共用資料庫-EF Core
description: 顯示如何在多個測試之間共用資料庫的範例
author: ajcvickers
ms.date: 04/25/2020
uid: core/miscellaneous/testing/sharing-databases
ms.openlocfilehash: 96216fb8afd6916402637de3ffab04b79d37e11c
ms.sourcegitcommit: 79e460f76b6664e1da5886d102bd97f651d2ffff
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82564277"
---
# <a name="sharing-databases-between-tests"></a><span data-ttu-id="ecbd9-103">在測試之間共用資料庫</span><span class="sxs-lookup"><span data-stu-id="ecbd9-103">Sharing databases between tests</span></span>

<span data-ttu-id="ecbd9-104">[EF Core 測試範例示範](xref:core/miscellaneous/testing/testing-sample)如何針對不同的資料庫系統測試應用程式。</span><span class="sxs-lookup"><span data-stu-id="ecbd9-104">The [EF Core testing sample](xref:core/miscellaneous/testing/testing-sample) showed how to test applications against different database systems.</span></span>
<span data-ttu-id="ecbd9-105">針對該範例，每個測試都會建立新的資料庫。</span><span class="sxs-lookup"><span data-stu-id="ecbd9-105">For that sample, each test created a new database.</span></span>
<span data-ttu-id="ecbd9-106">當使用 SQLite 或 EF 記憶體內部資料庫時，這是很好的模式，但是在使用其他資料庫系統時，可能會有相當大的負擔。</span><span class="sxs-lookup"><span data-stu-id="ecbd9-106">This is a good pattern when using SQLite or the EF in-memory database, but it can involve significant overhead when using other database systems.</span></span>

<span data-ttu-id="ecbd9-107">這個範例是以先前的範例為基礎，藉由將資料庫建立移至測試裝置。</span><span class="sxs-lookup"><span data-stu-id="ecbd9-107">This sample builds on the previous sample by moving database creation into a test fixture.</span></span>
<span data-ttu-id="ecbd9-108">如此一來，就可以為所有測試建立單一 SQL Server 資料庫，而且只植入一次。</span><span class="sxs-lookup"><span data-stu-id="ecbd9-108">This allows a single SQL Server database to be created and seeded only once for all tests.</span></span>

> [!TIP]
> <span data-ttu-id="ecbd9-109">請務必先完成[EF Core 測試範例](xref:core/miscellaneous/testing/testing-sample)，再繼續這裡。</span><span class="sxs-lookup"><span data-stu-id="ecbd9-109">Make sure to work through the [EF Core testing sample](xref:core/miscellaneous/testing/testing-sample) before continuing here.</span></span>

<span data-ttu-id="ecbd9-110">針對相同的資料庫撰寫多個測試並不容易。</span><span class="sxs-lookup"><span data-stu-id="ecbd9-110">It's not difficult to write multiple tests against the same database.</span></span>
<span data-ttu-id="ecbd9-111">這個技巧是以測試不會在執行的過程中執行的方式來進行。</span><span class="sxs-lookup"><span data-stu-id="ecbd9-111">The trick is doing it in a way that the tests don't trip over each other as they run.</span></span>
<span data-ttu-id="ecbd9-112">這需要瞭解：</span><span class="sxs-lookup"><span data-stu-id="ecbd9-112">This requires understanding:</span></span>
* <span data-ttu-id="ecbd9-113">如何安全地在測試之間共用物件</span><span class="sxs-lookup"><span data-stu-id="ecbd9-113">How to safely share objects between tests</span></span>
* <span data-ttu-id="ecbd9-114">當測試架構平行執行測試時</span><span class="sxs-lookup"><span data-stu-id="ecbd9-114">When the test framework runs tests in parallel</span></span>
* <span data-ttu-id="ecbd9-115">如何讓資料庫保持在每個測試的乾淨狀態</span><span class="sxs-lookup"><span data-stu-id="ecbd9-115">How to keep the database in a clean state for every test</span></span>  

## <a name="the-fixture"></a><span data-ttu-id="ecbd9-116">裝置</span><span class="sxs-lookup"><span data-stu-id="ecbd9-116">The fixture</span></span>

<span data-ttu-id="ecbd9-117">我們將使用測試裝置，在測試之間共用物件。</span><span class="sxs-lookup"><span data-stu-id="ecbd9-117">We will use a test fixture for sharing objects between tests.</span></span>
<span data-ttu-id="ecbd9-118">[XUnit 檔](https://xunit.net/docs/shared-context.html)說明當您想要建立單一測試內容並在類別中的所有測試之間共用時，應該使用的裝置，並在類別中的所有測試完成之後加以清除。</span><span class="sxs-lookup"><span data-stu-id="ecbd9-118">The [XUnit documentation](https://xunit.net/docs/shared-context.html) states that a fixture should be used "when you want to create a single test context and share it among all the tests in the class, and have it cleaned up after all the tests in the class have finished."</span></span>

> [!TIP]
> <span data-ttu-id="ecbd9-119">這個範例使用[XUnit](https://xunit.net/)，但其他測試架構中也有類似的概念，包括[NUnit](https://nunit.org/)。</span><span class="sxs-lookup"><span data-stu-id="ecbd9-119">This sample uses [XUnit](https://xunit.net/), but similar concepts exist in other testing frameworks, including [NUnit](https://nunit.org/).</span></span>

<span data-ttu-id="ecbd9-120">這表示我們需要將資料庫建立和植入移動到裝置類別。</span><span class="sxs-lookup"><span data-stu-id="ecbd9-120">This means that we need to move database creation and seeding to a fixture class.</span></span>
<span data-ttu-id="ecbd9-121">看起來如下：</span><span class="sxs-lookup"><span data-stu-id="ecbd9-121">Here's what it looks like:</span></span>

[!code-csharp[SharedDatabaseFixture](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/SharedDatabaseTests/SharedDatabaseFixture.cs?name=SharedDatabaseFixture)]

<span data-ttu-id="ecbd9-122">現在，請注意如何執行此函式：</span><span class="sxs-lookup"><span data-stu-id="ecbd9-122">For now, notice how the constructor:</span></span>
* <span data-ttu-id="ecbd9-123">建立裝置存留期的單一資料庫連線</span><span class="sxs-lookup"><span data-stu-id="ecbd9-123">Creates a single database connection for the lifetime of the fixture</span></span>
* <span data-ttu-id="ecbd9-124">藉由呼叫`Seed`方法來建立和植入該資料庫</span><span class="sxs-lookup"><span data-stu-id="ecbd9-124">Creates and seeds that database by calling the `Seed` method</span></span> 

<span data-ttu-id="ecbd9-125">立即忽略鎖定;我們稍後會再回來。</span><span class="sxs-lookup"><span data-stu-id="ecbd9-125">Ignore the locking for now; we will come back to it later.</span></span>

> [!TIP]
> <span data-ttu-id="ecbd9-126">建立和植入程式碼不需要是非同步。</span><span class="sxs-lookup"><span data-stu-id="ecbd9-126">The creation and seeding code does not need to be async.</span></span>
> <span data-ttu-id="ecbd9-127">讓它成為非同步會使程式碼變得複雜，而且不會改善效能或測試的輸送量。</span><span class="sxs-lookup"><span data-stu-id="ecbd9-127">Making it async will complicate the code and will not improve performance or throughput of tests.</span></span>

<span data-ttu-id="ecbd9-128">建立資料庫的方式是先刪除任何現有的資料庫，然後再建立新的資料庫。</span><span class="sxs-lookup"><span data-stu-id="ecbd9-128">The database is created by first deleting any existing database and then creating a new database.</span></span>
<span data-ttu-id="ecbd9-129">這可確保資料庫符合目前的 EF 模型，即使自上次測試執行之後已經變更。</span><span class="sxs-lookup"><span data-stu-id="ecbd9-129">This ensures that the database matches the current EF model even if it has been changed since the last test run.</span></span>

> [!TIP]
> <span data-ttu-id="ecbd9-130">使用類似[respawn](https://jimmybogard.com/tag/respawn/)的專案（而不是每次重新建立），可以更快速地「清理」現有的資料庫。</span><span class="sxs-lookup"><span data-stu-id="ecbd9-130">It can be faster to "clean" the existing database using something like [respawn](https://jimmybogard.com/tag/respawn/) rather than re-create it each time.</span></span>
> <span data-ttu-id="ecbd9-131">不過，執行此動作時，必須特別小心，以確保資料庫架構與 EF 模型是最新的。</span><span class="sxs-lookup"><span data-stu-id="ecbd9-131">However, care must be taken to ensure that the database schema is up-to-date with the EF model when doing this.</span></span>

<span data-ttu-id="ecbd9-132">處置裝置時，會處置資料庫連接。</span><span class="sxs-lookup"><span data-stu-id="ecbd9-132">The database connection is disposed when the fixture is disposed.</span></span>
<span data-ttu-id="ecbd9-133">您也可以考慮在此時刪除測試資料庫。</span><span class="sxs-lookup"><span data-stu-id="ecbd9-133">You may also consider deleting the test database at this point.</span></span>
<span data-ttu-id="ecbd9-134">不過，如果裝置正在由多個測試類別共用，這將需要額外的鎖定和參考計數。</span><span class="sxs-lookup"><span data-stu-id="ecbd9-134">However, this will require additional locking and reference counting if the fixture is being shared by multiple test classes.</span></span>
<span data-ttu-id="ecbd9-135">此外，讓測試資料庫仍然可以用於偵測失敗的測試通常會很有用。</span><span class="sxs-lookup"><span data-stu-id="ecbd9-135">Also, it is often useful to have the test database still available for debugging failed tests.</span></span>  

## <a name="using-the-fixture"></a><span data-ttu-id="ecbd9-136">使用裝置</span><span class="sxs-lookup"><span data-stu-id="ecbd9-136">Using the fixture</span></span>

<span data-ttu-id="ecbd9-137">XUnit 具有共同的模式，可將測試裝置與測試類別產生關聯：</span><span class="sxs-lookup"><span data-stu-id="ecbd9-137">XUnit has a common pattern for associating a test fixture with a class of tests:</span></span>

[!code-csharp[UsingTheFixture](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/SharedDatabaseTests/SharedDatabaseTest.cs?name=UsingTheFixture)]

<span data-ttu-id="ecbd9-138">XUnit 現在會建立單一裝置實例，並將它傳遞給測試類別的每個實例。</span><span class="sxs-lookup"><span data-stu-id="ecbd9-138">XUnit will now create a single fixture instance and pass it to each instance of the test class.</span></span>
<span data-ttu-id="ecbd9-139">（請記得第一個[測試範例](xref:core/miscellaneous/testing/testing-sample)，XUnit 會在每次執行測試時建立新的測試類別實例）。這表示資料庫會建立並植入一次，然後每個測試都會使用這個資料庫。</span><span class="sxs-lookup"><span data-stu-id="ecbd9-139">(Remember from the first [testing sample](xref:core/miscellaneous/testing/testing-sample) that XUnit creates a new test class instance every time it runs a test.) This means that the database will be created and seeded once and then each test will use this database.</span></span>

<span data-ttu-id="ecbd9-140">請注意，單一類別內的測試不會平行執行。</span><span class="sxs-lookup"><span data-stu-id="ecbd9-140">Note that tests within a single class will not be run in parallel.</span></span>
<span data-ttu-id="ecbd9-141">這表示，即使`DbConnection`物件不是安全線程，每個測試都可以使用相同的資料庫連接。</span><span class="sxs-lookup"><span data-stu-id="ecbd9-141">This means it is safe for each test to use the same database connection, even though the `DbConnection` object is not thread-safe.</span></span>

## <a name="maintaining-database-state"></a><span data-ttu-id="ecbd9-142">維護資料庫狀態</span><span class="sxs-lookup"><span data-stu-id="ecbd9-142">Maintaining database state</span></span>

<span data-ttu-id="ecbd9-143">測試通常需要使用插入、更新和刪除來改變測試資料。</span><span class="sxs-lookup"><span data-stu-id="ecbd9-143">Tests often need to mutate the test data with inserts, updates, and deletes.</span></span>
<span data-ttu-id="ecbd9-144">但是，這些變更會影響其他需要乾淨、植入之資料庫的測試。</span><span class="sxs-lookup"><span data-stu-id="ecbd9-144">But these changes will then impact other tests which are expecting a clean, seeded database.</span></span>

<span data-ttu-id="ecbd9-145">這可以藉由在交易內執行改變測試來處理。</span><span class="sxs-lookup"><span data-stu-id="ecbd9-145">This can be dealt with by running mutating tests inside a transaction.</span></span>
<span data-ttu-id="ecbd9-146">例如：</span><span class="sxs-lookup"><span data-stu-id="ecbd9-146">For example:</span></span>

[!code-csharp[CanAddItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/SharedDatabaseTests/SharedDatabaseTest.cs?name=CanAddItem)]

<span data-ttu-id="ecbd9-147">請注意，當測試完成時，就會建立交易並加以處置。</span><span class="sxs-lookup"><span data-stu-id="ecbd9-147">Notice that the transaction is created as the test starts and disposed when it is finished.</span></span>
<span data-ttu-id="ecbd9-148">處置交易會使其回復，因此其他測試將看不到任何變更。</span><span class="sxs-lookup"><span data-stu-id="ecbd9-148">Disposing the transaction causes it to be rolled back, so none of the changes will be seen by other tests.</span></span>

<span data-ttu-id="ecbd9-149">建立內容的 helper 方法（請參閱上述的裝置程式碼）會接受此交易，並選擇 DbCoNtext 使用它。</span><span class="sxs-lookup"><span data-stu-id="ecbd9-149">The helper method for creating a context (see the fixture code above) accepts this transaction and opts the DbContext into using it.</span></span> 

## <a name="sharing-the-fixture"></a><span data-ttu-id="ecbd9-150">共用裝置</span><span class="sxs-lookup"><span data-stu-id="ecbd9-150">Sharing the fixture</span></span>

<span data-ttu-id="ecbd9-151">您可能已注意到鎖定程式碼是關於建立和植入資料庫。</span><span class="sxs-lookup"><span data-stu-id="ecbd9-151">You may have noticed locking code around database creation and seeding.</span></span>
<span data-ttu-id="ecbd9-152">這個範例不需要這麼做，因為只有一個測試類別會使用裝置，因此只會建立單一裝置實例。</span><span class="sxs-lookup"><span data-stu-id="ecbd9-152">This is not needed for this sample since only one class of tests use the fixture, so only a single fixture instance is created.</span></span>

<span data-ttu-id="ecbd9-153">不過，您可能會想要將相同的裝置與多個測試類別搭配使用。</span><span class="sxs-lookup"><span data-stu-id="ecbd9-153">However, you may want to use the same fixture with multiple classes of tests.</span></span>
<span data-ttu-id="ecbd9-154">XUnit 會為每個類別建立一個裝置實例。</span><span class="sxs-lookup"><span data-stu-id="ecbd9-154">XUnit will create one fixture instance for each of these classes.</span></span>
<span data-ttu-id="ecbd9-155">這些可能會由平行執行測試的不同執行緒使用。</span><span class="sxs-lookup"><span data-stu-id="ecbd9-155">These may be used by different threads running tests in parallel.</span></span>
<span data-ttu-id="ecbd9-156">因此，請務必具有適當的鎖定，以確保只有一個執行緒會建立和植入資料庫。</span><span class="sxs-lookup"><span data-stu-id="ecbd9-156">Therefore, it is important to have appropriate locking to ensure only one thread does the database creation and seeding.</span></span>

> [!TIP]
> <span data-ttu-id="ecbd9-157">這裡有`lock`一個簡單的問題。</span><span class="sxs-lookup"><span data-stu-id="ecbd9-157">A simple `lock` is fine here.</span></span>
> <span data-ttu-id="ecbd9-158">不需要再嘗試任何複雜的作業，例如任何無鎖定模式。</span><span class="sxs-lookup"><span data-stu-id="ecbd9-158">There is no need to attempt anything more complex, such as any lock-free patterns.</span></span>
