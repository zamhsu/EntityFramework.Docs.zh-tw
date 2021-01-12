---
title: 在測試之間共用資料庫-EF Core
description: 顯示如何在多個測試之間共用資料庫的範例
author: ajcvickers
ms.date: 04/25/2020
uid: core/testing/sharing-databases
ms.openlocfilehash: 7a90a144271d5c34e9d5043aa439f84db805c6af
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128832"
---
# <a name="sharing-databases-between-tests"></a><span data-ttu-id="7ef3f-103">在測試之間共用資料庫</span><span class="sxs-lookup"><span data-stu-id="7ef3f-103">Sharing databases between tests</span></span>

<span data-ttu-id="7ef3f-104">[EF Core 測試範例示範](xref:core/testing/testing-sample)如何針對不同的資料庫系統測試應用程式。</span><span class="sxs-lookup"><span data-stu-id="7ef3f-104">The [EF Core testing sample](xref:core/testing/testing-sample) showed how to test applications against different database systems.</span></span>
<span data-ttu-id="7ef3f-105">針對該範例，每個測試都會建立新的資料庫。</span><span class="sxs-lookup"><span data-stu-id="7ef3f-105">For that sample, each test created a new database.</span></span>
<span data-ttu-id="7ef3f-106">在使用 SQLite 或 EF 記憶體內部資料庫時，這是很好的模式，但在使用其他資料庫系統時可能需要大量的負擔。</span><span class="sxs-lookup"><span data-stu-id="7ef3f-106">This is a good pattern when using SQLite or the EF in-memory database, but it can involve significant overhead when using other database systems.</span></span>

<span data-ttu-id="7ef3f-107">這個範例是以先前的範例為基礎，將資料庫建立移至測試裝置。</span><span class="sxs-lookup"><span data-stu-id="7ef3f-107">This sample builds on the previous sample by moving database creation into a test fixture.</span></span>
<span data-ttu-id="7ef3f-108">如此一來，就只會針對所有測試建立和植入單一 SQL Server 資料庫一次。</span><span class="sxs-lookup"><span data-stu-id="7ef3f-108">This allows a single SQL Server database to be created and seeded only once for all tests.</span></span>

> [!TIP]
> <span data-ttu-id="7ef3f-109">在這裡繼續進行之前，請務必先完成 [EF Core 測試範例](xref:core/testing/testing-sample) 。</span><span class="sxs-lookup"><span data-stu-id="7ef3f-109">Make sure to work through the [EF Core testing sample](xref:core/testing/testing-sample) before continuing here.</span></span>

<span data-ttu-id="7ef3f-110">針對相同資料庫撰寫多個測試並不難。</span><span class="sxs-lookup"><span data-stu-id="7ef3f-110">It's not difficult to write multiple tests against the same database.</span></span>
<span data-ttu-id="7ef3f-111">秘訣是以測試不會在執行時往返的方式來執行。</span><span class="sxs-lookup"><span data-stu-id="7ef3f-111">The trick is doing it in a way that the tests don't trip over each other as they run.</span></span>
<span data-ttu-id="7ef3f-112">這需要瞭解：</span><span class="sxs-lookup"><span data-stu-id="7ef3f-112">This requires understanding:</span></span>

* <span data-ttu-id="7ef3f-113">如何安全地在測試之間共用物件</span><span class="sxs-lookup"><span data-stu-id="7ef3f-113">How to safely share objects between tests</span></span>
* <span data-ttu-id="7ef3f-114">當測試架構平行執行測試時</span><span class="sxs-lookup"><span data-stu-id="7ef3f-114">When the test framework runs tests in parallel</span></span>
* <span data-ttu-id="7ef3f-115">如何針對每個測試讓資料庫保持乾淨狀態</span><span class="sxs-lookup"><span data-stu-id="7ef3f-115">How to keep the database in a clean state for every test</span></span>

## <a name="the-fixture"></a><span data-ttu-id="7ef3f-116">裝置</span><span class="sxs-lookup"><span data-stu-id="7ef3f-116">The fixture</span></span>

<span data-ttu-id="7ef3f-117">我們將使用測試裝置在測試之間共用物件。</span><span class="sxs-lookup"><span data-stu-id="7ef3f-117">We will use a test fixture for sharing objects between tests.</span></span>
<span data-ttu-id="7ef3f-118">[XUnit 檔](https://xunit.net/docs/shared-context.html)說明應該使用的裝置」。當您想要建立單一測試內容，並在類別中的所有測試之間共用時，並在類別中的所有測試完成後將它清除。</span><span class="sxs-lookup"><span data-stu-id="7ef3f-118">The [XUnit documentation](https://xunit.net/docs/shared-context.html) states that a fixture should be used "when you want to create a single test context and share it among all the tests in the class, and have it cleaned up after all the tests in the class have finished."</span></span>

> [!TIP]
> <span data-ttu-id="7ef3f-119">此範例會使用 [XUnit](https://xunit.net/)，但其他測試架構（包括 [NUnit](https://nunit.org/)）中也有類似的概念。</span><span class="sxs-lookup"><span data-stu-id="7ef3f-119">This sample uses [XUnit](https://xunit.net/), but similar concepts exist in other testing frameworks, including [NUnit](https://nunit.org/).</span></span>

<span data-ttu-id="7ef3f-120">這表示我們需要將資料庫建立和植入移至裝置類別。</span><span class="sxs-lookup"><span data-stu-id="7ef3f-120">This means that we need to move database creation and seeding to a fixture class.</span></span>
<span data-ttu-id="7ef3f-121">其看起來會像下面這樣：</span><span class="sxs-lookup"><span data-stu-id="7ef3f-121">Here's what it looks like:</span></span>

[!code-csharp[SharedDatabaseFixture](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/SharedDatabaseTests/SharedDatabaseFixture.cs?name=SharedDatabaseFixture)]

<span data-ttu-id="7ef3f-122">現在，請注意函式如何：</span><span class="sxs-lookup"><span data-stu-id="7ef3f-122">For now, notice how the constructor:</span></span>

* <span data-ttu-id="7ef3f-123">在裝置的存留期內建立單一資料庫連接</span><span class="sxs-lookup"><span data-stu-id="7ef3f-123">Creates a single database connection for the lifetime of the fixture</span></span>
* <span data-ttu-id="7ef3f-124">藉由呼叫方法來建立和植入資料庫 `Seed`</span><span class="sxs-lookup"><span data-stu-id="7ef3f-124">Creates and seeds that database by calling the `Seed` method</span></span>

<span data-ttu-id="7ef3f-125">立即忽略鎖定;我們稍後會再回來。</span><span class="sxs-lookup"><span data-stu-id="7ef3f-125">Ignore the locking for now; we will come back to it later.</span></span>

> [!TIP]
> <span data-ttu-id="7ef3f-126">建立和植入程式碼不需要是非同步。</span><span class="sxs-lookup"><span data-stu-id="7ef3f-126">The creation and seeding code does not need to be async.</span></span>
> <span data-ttu-id="7ef3f-127">使其非同步會使程式碼變得複雜，而不會改善測試的效能或輸送量。</span><span class="sxs-lookup"><span data-stu-id="7ef3f-127">Making it async will complicate the code and will not improve performance or throughput of tests.</span></span>

<span data-ttu-id="7ef3f-128">建立資料庫的方式是先刪除任何現有的資料庫，然後再建立新的資料庫。</span><span class="sxs-lookup"><span data-stu-id="7ef3f-128">The database is created by first deleting any existing database and then creating a new database.</span></span>
<span data-ttu-id="7ef3f-129">這樣可確保資料庫符合目前的 EF 模型，即使自上次執行測試之後已經變更。</span><span class="sxs-lookup"><span data-stu-id="7ef3f-129">This ensures that the database matches the current EF model even if it has been changed since the last test run.</span></span>

> [!TIP]
> <span data-ttu-id="7ef3f-130">您可以使用 [respawn](https://jimmybogard.com/tag/respawn/) 之類的方式，以較快的方式「清理」現有的資料庫，而不是每次重新建立它。</span><span class="sxs-lookup"><span data-stu-id="7ef3f-130">It can be faster to "clean" the existing database using something like [respawn](https://jimmybogard.com/tag/respawn/) rather than re-create it each time.</span></span>
> <span data-ttu-id="7ef3f-131">但是，在執行這項操作時，必須小心確保資料庫架構是與 EF 模型保持最新的狀態。</span><span class="sxs-lookup"><span data-stu-id="7ef3f-131">However, care must be taken to ensure that the database schema is up-to-date with the EF model when doing this.</span></span>

<span data-ttu-id="7ef3f-132">處置裝置時，就會處置資料庫連接。</span><span class="sxs-lookup"><span data-stu-id="7ef3f-132">The database connection is disposed when the fixture is disposed.</span></span>
<span data-ttu-id="7ef3f-133">您也可以考慮在此時刪除測試資料庫。</span><span class="sxs-lookup"><span data-stu-id="7ef3f-133">You may also consider deleting the test database at this point.</span></span>
<span data-ttu-id="7ef3f-134">但是，如果多個測試類別正在共用這些裝置，則這將需要額外的鎖定和參考計數。</span><span class="sxs-lookup"><span data-stu-id="7ef3f-134">However, this will require additional locking and reference counting if the fixture is being shared by multiple test classes.</span></span>
<span data-ttu-id="7ef3f-135">此外，讓測試資料庫仍可用於偵測失敗的測試通常會很有用。</span><span class="sxs-lookup"><span data-stu-id="7ef3f-135">Also, it is often useful to have the test database still available for debugging failed tests.</span></span>

## <a name="using-the-fixture"></a><span data-ttu-id="7ef3f-136">使用裝置</span><span class="sxs-lookup"><span data-stu-id="7ef3f-136">Using the fixture</span></span>

<span data-ttu-id="7ef3f-137">XUnit 具有共同的模式，可將測試裝置與測試類別產生關聯：</span><span class="sxs-lookup"><span data-stu-id="7ef3f-137">XUnit has a common pattern for associating a test fixture with a class of tests:</span></span>

[!code-csharp[UsingTheFixture](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/SharedDatabaseTests/SharedDatabaseTest.cs?name=UsingTheFixture)]

<span data-ttu-id="7ef3f-138">XUnit 現在會建立單一裝置實例，並將它傳遞給測試類別的每個實例。</span><span class="sxs-lookup"><span data-stu-id="7ef3f-138">XUnit will now create a single fixture instance and pass it to each instance of the test class.</span></span>
<span data-ttu-id="7ef3f-139"> (記得第一個 [測試範例](xref:core/testing/testing-sample) ，XUnit 會在每次執行測試時建立新的測試類別實例。 ) 這表示資料庫將建立並植入一次，然後每個測試都會使用此資料庫。</span><span class="sxs-lookup"><span data-stu-id="7ef3f-139">(Remember from the first [testing sample](xref:core/testing/testing-sample) that XUnit creates a new test class instance every time it runs a test.) This means that the database will be created and seeded once and then each test will use this database.</span></span>

<span data-ttu-id="7ef3f-140">請注意，單一類別內的測試不會以平行方式執行。</span><span class="sxs-lookup"><span data-stu-id="7ef3f-140">Note that tests within a single class will not be run in parallel.</span></span>
<span data-ttu-id="7ef3f-141">這表示，每個測試都可以使用相同的資料庫連接，即使 `DbConnection` 物件不是安全線程也是一樣。</span><span class="sxs-lookup"><span data-stu-id="7ef3f-141">This means it is safe for each test to use the same database connection, even though the `DbConnection` object is not thread-safe.</span></span>

## <a name="maintaining-database-state"></a><span data-ttu-id="7ef3f-142">維護資料庫狀態</span><span class="sxs-lookup"><span data-stu-id="7ef3f-142">Maintaining database state</span></span>

<span data-ttu-id="7ef3f-143">測試通常需要使用插入、更新和刪除來改變測試資料。</span><span class="sxs-lookup"><span data-stu-id="7ef3f-143">Tests often need to mutate the test data with inserts, updates, and deletes.</span></span>
<span data-ttu-id="7ef3f-144">但是這些變更會影響其他需要乾淨、植入資料庫的測試。</span><span class="sxs-lookup"><span data-stu-id="7ef3f-144">But these changes will then impact other tests which are expecting a clean, seeded database.</span></span>

<span data-ttu-id="7ef3f-145">在交易內執行改變測試，即可處理此情況。</span><span class="sxs-lookup"><span data-stu-id="7ef3f-145">This can be dealt with by running mutating tests inside a transaction.</span></span>
<span data-ttu-id="7ef3f-146">例如：</span><span class="sxs-lookup"><span data-stu-id="7ef3f-146">For example:</span></span>

[!code-csharp[CanAddItem](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/SharedDatabaseTests/SharedDatabaseTest.cs?name=CanAddItem)]

<span data-ttu-id="7ef3f-147">請注意，當測試開始時，即會建立交易，並在完成時處置。</span><span class="sxs-lookup"><span data-stu-id="7ef3f-147">Notice that the transaction is created as the test starts and disposed when it is finished.</span></span>
<span data-ttu-id="7ef3f-148">處置交易會導致其回復，因此其他測試將看不到任何變更。</span><span class="sxs-lookup"><span data-stu-id="7ef3f-148">Disposing the transaction causes it to be rolled back, so none of the changes will be seen by other tests.</span></span>

<span data-ttu-id="7ef3f-149">建立內容的 helper 方法 (查看上述的裝置程式碼) 接受此交易並選擇 DbCoNtext 使用它。</span><span class="sxs-lookup"><span data-stu-id="7ef3f-149">The helper method for creating a context (see the fixture code above) accepts this transaction and opts the DbContext into using it.</span></span>

## <a name="sharing-the-fixture"></a><span data-ttu-id="7ef3f-150">共用裝置</span><span class="sxs-lookup"><span data-stu-id="7ef3f-150">Sharing the fixture</span></span>

<span data-ttu-id="7ef3f-151">您可能已注意到有關建立和植入資料庫的鎖定程式碼。</span><span class="sxs-lookup"><span data-stu-id="7ef3f-151">You may have noticed locking code around database creation and seeding.</span></span>
<span data-ttu-id="7ef3f-152">這個範例並不需要這麼做，因為只有一個測試類別會使用此裝置，因此只會建立單一裝置實例。</span><span class="sxs-lookup"><span data-stu-id="7ef3f-152">This is not needed for this sample since only one class of tests use the fixture, so only a single fixture instance is created.</span></span>

<span data-ttu-id="7ef3f-153">不過，您可能會想要在多個測試類別中使用相同的裝置。</span><span class="sxs-lookup"><span data-stu-id="7ef3f-153">However, you may want to use the same fixture with multiple classes of tests.</span></span>
<span data-ttu-id="7ef3f-154">XUnit 會為每個類別建立一個裝置實例。</span><span class="sxs-lookup"><span data-stu-id="7ef3f-154">XUnit will create one fixture instance for each of these classes.</span></span>
<span data-ttu-id="7ef3f-155">這些可能會由不同的執行緒平行執行測試使用。</span><span class="sxs-lookup"><span data-stu-id="7ef3f-155">These may be used by different threads running tests in parallel.</span></span>
<span data-ttu-id="7ef3f-156">因此，請務必具有適當的鎖定，以確保只有一個執行緒會建立和植入資料庫。</span><span class="sxs-lookup"><span data-stu-id="7ef3f-156">Therefore, it is important to have appropriate locking to ensure only one thread does the database creation and seeding.</span></span>

> [!TIP]
> <span data-ttu-id="7ef3f-157">這裡有一個簡單的 `lock` 問題。</span><span class="sxs-lookup"><span data-stu-id="7ef3f-157">A simple `lock` is fine here.</span></span>
> <span data-ttu-id="7ef3f-158">不需要再嘗試任何更複雜的作業，例如任何無鎖定模式。</span><span class="sxs-lookup"><span data-stu-id="7ef3f-158">There is no need to attempt anything more complex, such as any lock-free patterns.</span></span>
